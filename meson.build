project(
  'vte', ['c', 'cpp'],
  version: '0.49.2',
  license: 'LGPL2.1',
  default_options: [
    'buildtype=debugoptimized',
    'cpp_std=c++11',
    'warning_level=1'
  ],
  meson_version: '>= 0.41.0'
)

vte_version = meson.project_version()
version_array = vte_version.split('.')
vte_major_version = version_array[0].to_int()
vte_minor_version = version_array[1].to_int()
vte_micro_version = version_array[2].to_int()

vte_gir_ns = 'Vte'
vte_gir_version = '1.0'

enable_gir = get_option('enable-introspection')
enable_vala = get_option('enable-vala')
enable_test_application = get_option('enable-test-application')
enable_glade_catalogue = get_option('enable-glade-catalogue')

if get_option('with-gtk') == '3.0'
  gtk_api_version = '3.0'
  gtk_req_version = '>= 3.8.0'
  vte_api_version = '2.91'
  glade_dep_name = 'gladeui-2.0'
else
  gtk_api_version = '4.0'
  gtk_req_version = '>= 3.89.0'
  vte_api_version = '3.91'
  # glade_dep_name = 'gladeui-4.0' ?
  # remove the following when they're ready
  enable_gir = false
  enable_vala = false
  enable_test_application = false
  enable_glade_catalogue = false
endif

gtk_dep_name = 'gtk+-' + gtk_api_version

vte_api_name = '@0@-@1@'.format(meson.project_name(), vte_api_version)
vte_api_path = join_paths(vte_api_name, meson.project_name())

vte_prefix = get_option('prefix')
vte_bindir = join_paths(vte_prefix, get_option('bindir'))
vte_datadir = join_paths(vte_prefix, get_option('datadir'))
vte_libdir = join_paths(vte_prefix, get_option('libdir'))
vte_libexecdir = join_paths(vte_prefix, get_option('libexecdir'))
vte_localedir = join_paths(vte_prefix, get_option('localedir'))
vte_includedir = join_paths(vte_prefix, get_option('includedir'))
vte_sysconfdir = join_paths(vte_prefix, get_option('sysconfdir'))

vte_pkgincludedir = join_paths(vte_includedir, vte_api_name, meson.project_name())

soversion = 0

revision = (vte_minor_version.is_odd() ? 0 : vte_micro_version)
age = vte_minor_version * 100 + vte_micro_version - revision
current = vte_major_version + age

libversion = '@0@.@1@.@2@'.format(soversion, current, revision)

vte_debug = get_option('buildtype').contains('debug')

cc = meson.get_compiler('c')
cxx = meson.get_compiler('cpp')

config_h = configuration_data()

# defines
set_defines = [
  # package
  ['PACKAGE', meson.project_name()],
  ['PACKAGE_BUGREPORT', 'http://bugzilla.gnome.org/enter_bug.cgi?product=' + meson.project_name()],
  ['PACKAGE_NAME', meson.project_name()],
  ['PACKAGE_STRING', '@0@ @1@'.format(meson.project_name(), vte_version)],
  ['PACKAGE_TARNAME', meson.project_name()],
  ['PACKAGE_URL', ''],
  ['PACKAGE_VERSION', vte_version],
  ['VERSION', vte_version],
  # i18n
  ['GETTEXT_PACKAGE', vte_api_name]
]

foreach define: set_defines
  config_h.set_quoted(define[0], define[1])
endforeach

config_h.set_quoted('VTE_DEFAULT_TERM', 'xterm-256color',
                    description: 'The default value $TERM is set to.')

config_h.set('VTE_DEBUG', vte_debug,
             description: 'Enable debugging messages.')

# headers
check_headers = [
  ['HAVE_DLFCN_H', 'dlfcn.h'],
  ['HAVE_INTTYPES_H', 'inttypes.h'],
  ['HAVE_LOCALE_H', 'locale.h'],
  ['HAVE_MEMORY_H', 'memory.h'],
  ['HAVE_PTY_H', 'pty.h'],
  ['HAVE_STDINT_H', 'stdint.h'],
  ['HAVE_STDLIB_H', 'stdlib.h'],
  ['HAVE_STRINGS_H', 'strings.h'],
  ['HAVE_STRING_H', 'string.h'],
  ['HAVE_STROPTS_H', 'stropts.h'],
  ['HAVE_SYS_SELECT_H', 'sys/select.h'],
  ['HAVE_SYS_STAT_H', 'sys/stat.h'],
  ['HAVE_SYS_SYSLIMITS_H', 'sys/syslimits.h'],
  ['HAVE_SYS_TERMIOS_H', 'sys/termios.h'],
  ['HAVE_SYS_TYPES_H', 'sys/types.h'],
  ['HAVE_SYS_WAIT_H', 'sys/wait.h'],
  ['HAVE_TERMIOS_H', 'termios.h'],
  ['HAVE_UNISTD_H', 'unistd.h'],
  ['HAVE_UTIL_H', 'util.h'],
  ['HAVE_WCHAR_H', 'wchar.h'],
  # for vtespawn
  ['HAVE_SYS_RESOURCE_H', 'sys/resource.h']
]

foreach header: check_headers
  config_h.set(header[0], cc.has_header(header[1]))
endforeach

# functions
check_functions = [
  # i18n
  ['HAVE_DCGETTEXT', 'dcgettext'],
  ['HAVE_GETTEXT', 'gettext'],
  # Check for how to open a new PTY. We support posix_openpt and BSDs openpty
  ['HAVE_GRANTPT', 'grantpt'],
  ['HAVE_POSIX_OPENPT', 'posix_openpt'],
  ['HAVE_PTSNAME', 'ptsname'],
  ['HAVE_UNLOCKPT', 'unlockpt'],
  # Misc PTY handling functions
  ['HAVE_CFMAKERAW', 'cfmakeraw'],
  ['HAVE_FORK', 'fork'],
  ['HAVE_GETPGID', 'getpgid'],
  ['HAVE_SETPGID', 'setpgid'],
  ['HAVE_SETSID', 'setsid'],
  ['HAVE_TCGETATTR', 'tcgetattr'],
  ['HAVE_TCSETATTR', 'tcsetattr'],
  # Misc I/O routines.
  ['HAVE_PREAD', 'pread'],
  ['HAVE_PWRITE', 'pwrite'],
  ['HAVE_EXPLICIT_BZERO', 'explicit_bzero'],
  # for vtespawn
  ['HAVE_FDWALK', 'fdwalk']
]

if host_machine.system().contains('darwin')
  check_functions += [
    ['HAVE_CFLOCALECOPYCURRENT', 'CFLocaleCopyCurrent'],
    ['HAVE_CFPREFERENCESCOPYAPPVALUE', 'CFPreferencesCopyAppValue']
  ]
endif

foreach func: check_functions
  config_h.set(func[0], cc.has_function(func[1]))
endforeach

# Math functions
m_deps = []
if not cc.has_function('floor')
  m_deps += cc.find_library('m', required: false)
endif

config_h.set('HAVE_CEIL', cc.has_function('ceil', dependencies: m_deps))
config_h.set('HAVE_FLOOR', cc.has_function('floor', dependencies: m_deps))
config_h.set('HAVE_ROUND', cc.has_function('round', dependencies: m_deps))

# symbols
check_symbols = [
  ['GWINSZ_IN_SYS_IOCTL', 'sys/ioctl.h', 'TIOCGWINSZ'],
  ['HAVE_LC_MESSAGES', 'locale.h', 'LC_MESSAGES'],
  ['HAVE_WINT_T', 'wchar.h', 'wint_t']
]

foreach symbol: check_symbols
  config_h.set(symbol[0], cc.has_header_symbol(symbol[1], symbol[2]))
endforeach

# i18n
have_bind_textdomain_codeset = cc.has_header_symbol('libintl.h', 'bind_textdomain_codeset')
config_h.set('HAVE_BIND_TEXTDOMAIN_CODESET', have_bind_textdomain_codeset)
config_h.set('HAVE_DECL_BIND_TEXTDOMAIN_CODESET', have_bind_textdomain_codeset)

# compiler flags
common_flags = ['-DHAVE_CONFIG_H']
common_c_flags = []
common_cxx_flags = []

if vte_debug
  common_test_flags = [
    '-fno-common',
    '-fdiagnostics-show-option',
    '-fno-semantic-interposition',
    '-fno-strict-aliasing',
    '-fstack-protector',
    '-fstack-protector-strong',
    '-pipe',
    '-Wcast-align',
    '-Wendif-labels',
    '-Werror=declaration-after-statement',
    '-Werror=format=2',
    '-Werror=format-nonliteral',
    '-Werror=format-security',
    '-Werror=implicit-function-declaration',
    '-Werror=init-self',
    '-Werror=missing-include-dirs',
    '-Werror=missing-prototypes',
    '-Werror=pointer-arith',
    '-Wfloat-equal',
    '-Wlogical-op',
    '-Wmisleading-indentation',
    '-Wmissing-declarations',
    '-Wmissing-include-dirs',
    '-Wmissing-format-attribute',
    '-Wmissing-noreturn',
    '-Wno-deprecated-declarations',
    '-Wno-missing-field-initializers',
    '-Wno-switch-enum',
    '-Wno-unused-parameter',
    '-Wno-packed',
    '-Wshadow',
    '-Wstrict-aliasing=2',
    '-Wundef',
    '-Wunsafe-loop-optimizations',
    '-Wwrite-strings'
  ]

  test_flags = [
    '-Waggregate-return',
    '-Wnested-externs',
    '-Wstrict-prototypes'
  ]

  foreach flag: common_test_flags + test_flags
    if cc.has_argument(flag)
      common_c_flags += [flag]
    endif
  endforeach

  flag = '-fvisibility=hidden'
  assert(cxx.has_argument(flag), cxx.get_id() + ' doesn\'t support required flag -fvisibility=hidden')
  common_cxx_flags += [flag]

  test_flags = [
    '-fno-rtti',
    '-fno-exceptions',
    '-fvisibility-inlines-hidden',
    '-Wstrict-null-sentinel'
  ]

  foreach flag: common_test_flags + test_flags
    if cxx.has_argument(flag)
      common_cxx_flags += [flag]
    endif
  endforeach
endif

add_project_arguments(common_flags + common_c_flags, language: 'c')
add_project_arguments(common_flags + common_cxx_flags, language: 'cpp')

enable_b_symbolic = get_option('enable-Bsymbolic')
if enable_b_symbolic != 'no'
  ldflag = '-Wl,-Bsymbolic-functions'

  if host_machine.system().contains('linux') and cc.has_argument(ldflag)
    add_project_link_arguments(ldflag, language: 'c')
  else
    if enable_b_symbolic == 'yes'
      error('-Bsymbolic requested but not supported by ld. Use -Densable-Bsymbolic=false to disable')
    endif
    message('-Bsymbolic not supported by ld; disabling')
  endif

  if host_machine.system().contains('linux') and cxx.has_argument(ldflag)
    add_project_link_arguments(ldflag, language: 'cpp')
  else
    if enable_b_symbolic == 'yes'
      error('-Bsymbolic requested but not supported by cxx ld. Use -Denable-Bsymbolic=false to disable')
    endif
    message('-Bsymbolic not supported by ld; disabling')
  endif
endif

glib_req_version = '>= 2.40.0'
pango_req_version = '>= 1.22.0'
libpcre_req_version = '>= 10.21'
gnutls_req_version = '>= 3.2.7'

glib_dep = dependency('glib-2.0', version: glib_req_version)
gobject_dep = dependency('gobject-2.0')

gtk_dep = dependency(gtk_dep_name, version: gtk_req_version)
have_gtk_3_16 = gtk_dep.version().version_compare('>= 3.16')

vte_deps = m_deps + [
  glib_dep,
  gobject_dep,
  gtk_dep,
  dependency('gio-2.0'),
  dependency('gio-unix-2.0'),
  dependency('libpcre2-8', version: libpcre_req_version),
  dependency('pango', version: pango_req_version),
  dependency('threads'),
  dependency('zlib')
]

enable_gnutls = get_option('enable-gnutls')
if enable_gnutls
  vte_deps += dependency('gnutls', version: gnutls_req_version)
endif

config_h.set('WITH_GNUTLS', enable_gnutls,
             description: 'Define to 1 to enable gnutls support')

configure_file(
  output: 'config.h',
  configuration: config_h
)

gnome = import('gnome')
i18n = import('i18n')
pkg = import('pkgconfig')

po_dir = join_paths(meson.source_root(), 'po')

top_inc = include_directories('.')

subdir('src')

# introspection support
if enable_gir
  gir_dep = dependency('gobject-introspection-1.0', version: '>= 0.9.0', required: false)
  assert(gir_dep.found(), 'Introspection requested but gobject-introspection-1.0 not installed. Try -Denable-introspection=false')

  subdir('bindings/gir')
endif

# vala support
if enable_vala
  assert(enable_gir, 'you need introspection support for the vala support')

  vala_req_version = '>= 0.24.0'
  error_str = 'Vala requested but vala compiler ' + vala_req_version + ' not found. Try -Denable-vala=false'

  assert(add_languages('vala', required: false), error_str)
  valac_version = meson.get_compiler('vala').version()
  assert(valac_version.version_compare(vala_req_version), error_str)

  have_vala_0_31 = valac_version.version_compare('>= 0.31.1')

  subdir('bindings/vala')
endif

subdir('po')

if get_option('enable-gtk-doc')
  subdir('doc/openi18n')
  subdir('doc/reference')
endif

if enable_glade_catalogue
  glade_dep = dependency(glade_dep_name)
  assert(glade_dep.found(), glade_dep_name + ' not found')

  glade_catalogue_dir = glade_dep.get_pkgconfig_variable('catalogdir')
  glade_icon_dir = glade_dep.get_pkgconfig_variable('pixmapdir')
  glade_module_dir = glade_dep.get_pkgconfig_variable('moduledir')

  # FIXME icon copy/rename is not supported yet
  #       https://github.com/mesonbuild/meson/issues/1487
  subdir('glade')
endif

output = 'Configuration for libvte ' + vte_version + ' for ' + gtk_dep_name + '\n'
output += '        GNUTLS: ' + enable_gnutls.to_string() + '\n'
output += '        Installing Glade catalogue: ' + enable_glade_catalogue.to_string() + '\n'
output += '        Introspection: ' + enable_glade_catalogue.to_string() + '\n'
output += '        Vala binginds: ' + enable_vala.to_string() + '\n'
output += '        Test application: ' + enable_test_application.to_string()
message(output)
