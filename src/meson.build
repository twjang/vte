subdir('vte')

src_inc = include_directories('.')

vtetype_enums = [vtetype_header]
vtetype_enums += gnome.mkenums(
  vtetype + '.cc',
  sources: enum_headers,
  c_template: vtetype + '.cc.template'
)

gir_sources = files(
  'pty.cc',
  'vtegtk.cc'
)

sources = vtetype_enums + gir_sources + files(
  'caps.cc',
  'debug.cc',
  'iso2022.cc',
  'keymap.cc',
  'matcher.cc',
  'reaper.cc',
  'ring.cc',
  'table.cc',
  'vte.cc',
  'vteaccess.cc',
  'vteconv.cc',
  'vtedraw.cc',
  'vteregex.cc',
  'vterowdata.cc',
  'vtespawn.cc',
  'vteseq.cc',
  'vtestream.cc',
  'vtetree.cc',
  'vtetypes.cc',
  'vteunistr.cc',
  'vteutils.cc'
)

box_drawing = 'box_drawing'

sources += custom_target(
  box_drawing,
  input: box_drawing + '.txt',
  output: box_drawing + '.h',
  capture: true,
  command: [find_program('box_drawing_generate.sh'), '@INPUT@']
)

sources += gnome.compile_resources(
  'vteresources',
  'vte.gresource.xml.in',
  source_dir: '.',
  c_name: '_vte',
  export: true
)

sources += gnome.genmarshal(
  'marshal',
  sources: 'marshal.list',
  prefix: '_vte_marshal'
)

vteseq_gperf = 'vteseq-n.gperf'

vteseq_cc = 'vteseq-n.cc'

# FIXME: meson tries to compile it due to the .cc extension.
# build_by_default is used as a workaround.
custom_target(
  vteseq_cc,
  input: vteseq_gperf,
  output: vteseq_cc,
  capture: true,
  command: [find_program('gperf'), '-m', '100', '@INPUT@'],
  build_by_default: true
)

vteseq_list = 'vteseq-list.h'

sources += custom_target(
  vteseq_list,
  input: vteseq_gperf,
  output: vteseq_list,
  capture: true,
  command: [find_program('vteseq-n-list-gen.sh'), '@INPUT@']
)

cflags = [
  '-DDATADIR="@0@"'.format(vte_datadir),
  '-DG_LOG_DOMAIN="Vte"',
  '-DGLIB_DISABLE_DEPRECATION_WARNINGS',
  '-DLIBEXECDIR="@0@"'.format(vte_libexecdir),
  '-DLOCALEDIR="@0@"'.format(vte_localedir),
  '-DVTE_API_VERSION="@0@"'.format(vte_api_version),
  '-DVTE_COMPILATION'
]

libvte = shared_library(
  'vte-' + vte_api_version,
  sources: sources,
  version: libversion,
  include_directories: [top_inc, vte_inc],
  dependencies: vte_deps,
  cpp_args: cflags,
  install: true,
  install_dir: vte_libdir
)

libvte_dep = declare_dependency(
  link_with: libvte,
  include_directories: [src_inc, vte_inc],
  dependencies: vte_deps
)

pkg.generate(
  libraries: libvte,
  version: vte_version,
  name: meson.project_name(),
  description: 'Vte terminal widget.',
  filebase: vte_api_name,
  subdirs: vte_api_name,
  requires: [
    'glib-2.0 ' + glib_req_version,
    'gobject-2.0',
    'pango ' + pango_req_version,
    gtk_dep_name + ' ' + gtk_req_version,
    'gio-2.0',
    'gio-unix-2.0',
    'zlib',
    'libpcre2-8 ' + libpcre_req_version,
    'gnutls ' + gnutls_req_version
  ],
  variables: 'exec_prefix=' + vte_libexecdir,
  install_dir: join_paths(vte_libdir, 'pkgconfig')
)

# C Test application
testvte_sources = [vtetype_header]
testvte_sources += files(
  'debug.c',
  'vteapp.c'
)

testvte_cflags = [
  '-DGLIB_DISABLE_DEPRECATION_WARNINGS',
  '-DGDK_DISABLE_DEPRECATION_WARNINGS'
]

# Misc unit tests and utilities
interpret_sources = files(
  'caps.cc',
  'debug.cc',
  'iso2022.cc',
  'matcher.cc',
  'table.cc',
  'vteconv.cc',
  'vtetree.cc',
  'interpret.c'
)

interpret_deps = [
  glib_dep,
  gobject_dep,
  gtk_dep
]

interpret_cflags = [
  '-DINTERPRET_MAIN',
  '-DVTE_API_VERSION="@0@"'.format(vte_api_version)
]

reaper_sources = files(
  'debug.cc',
  'reaper.cc'
)

table_sources = files(
  'caps.cc',
  'debug.cc',
  'matcher.cc',
  'table.cc',
  'vteconv.cc'
)

vtestream_file_sources = files(
  'vtestream.cc',
  'vteutils.cc'
)

programs = [
  # name, sources, dependencies, cflags, cxxflags
  ['testvte', testvte_sources, [libvte_dep], testvte_cflags, []],
  ['interpret', interpret_sources, interpret_deps, [], interpret_cflags],
  ['slowcat', 'slowcat.c', [glib_dep], [], []],
  ['dumpkeys', 'dumpkeys.c', [glib_dep], [], []],
  ['reaper', reaper_sources, vte_deps, [], ['-DMAIN']],
  ['reflect-text-view', 'reflect.c', [libvte_dep], ['-DUSE_TEXT_VIEW'], []],
  ['reflect-vte', 'reflect.c', [libvte_dep], ['-DUSE_VTE'], []],
  ['mev', 'mev.c', [glib_dep], [], []],
  ['table', table_sources, [gobject_dep, glib_dep], [], ['-DTABLE_MAIN']],
  ['xticker', 'xticker.c', [glib_dep], [], []],
  ['vteconv', 'vteconv.cc', vte_deps, [], ['-DVTECONV_MAIN']],
  ['vtestream-file', vtestream_file_sources, vte_deps, [], ['-DVTESTREAM_MAIN']],
  ['test-vtetypes', 'vtetypes.cc', vte_deps, [], ['-DMAIN']]
]

foreach program: programs
  executable(
    program[0],
    program[1],
    include_directories: top_inc,
    dependencies: program[2],
    c_args: program[3],
    cpp_args: program[4]
  )
endforeach

test_env = [
  'srcdir=' + meson.current_source_dir(),
  'top_builddir=' + meson.build_root(),
  'VTE_API_VERSION="@0@"'.format(vte_api_version)
]

unit_tests = [
  # FIXME: the test fails due to two reasons
  # 1. set: -i: invalid option
  # 2. __vte_urlencode: command not found
  #'test-vte-sh.sh',
  'check-doc-syntax.sh'
]

foreach unit: unit_tests
  test(
    unit,
    find_program(unit),
    env: test_env
  )
endforeach

install_data(
  'vte.sh',
  install_dir: join_paths(vte_sysconfdir, 'profile.d')
)
