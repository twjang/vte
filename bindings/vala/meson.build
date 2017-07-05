deps = [
  'gio-2.0',
  'glib-2.0',
  'gdk-' + gtk_api_version,
  gtk_dep_name,
  'pango'
]

libvte_vapi = gnome.generate_vapi(
  vte_api_name,
  sources: libvte_gir[0],
  packages: deps,
  install: true,
  install_dir: join_paths(vte_datadir, 'vala', 'vapi')
)

# VALA Test application
sources = files(
  'app.vala',
  'config.vapi'
)

deps = files(
  'app.ui',
  'search-popover.ui'
)

sources += gnome.compile_resources(
  'appresources',
  'app.gresource.xml',
  source_dir: '.',
  c_name: 'app',
  dependencies: deps,
  export: true
)

incs = [
  top_inc,
  src_inc,
  vte_inc
]

deps = [
  gtk_dep,
  libvte_vapi,
  dependency('gio-2.0')
]

flags = [
  '-Wno-unused-but-set-variable',
  '-Wno-unused-variable'
]

args = [
  '--target-glib=2.38',
  '--pkg=posix'
]

if have_vala_0_31
  args += '--disable-since-check'
endif

if have_gtk_3_16
  args += '--define=GTK_3_16'
endif

executable(
  vte_api_name,
  sources: sources,
  include_directories: incs,
  dependencies: deps,
  c_args: flags,
  vala_args: args,
  install: enable_test_application,
  install_dir: vte_bindir
)
