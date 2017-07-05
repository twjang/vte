vte_inc = include_directories('.')

enum_headers = files(
  'vtedeprecated.h',
  'vteenums.h'
)

headers = enum_headers + files(
  'vte.h',
  'vteglobals.h',
  'vtemacros.h',
  'vtepty.h',
  'vteregex.h',
  'vteterminal.h'
)

vteversion_conf = configuration_data()
vteversion_conf.set('VTE_MAJOR_VERSION', vte_major_version)
vteversion_conf.set('VTE_MINOR_VERSION', vte_minor_version)
vteversion_conf.set('VTE_MICRO_VERSION', vte_micro_version)

vteversion = 'vteversion.h'

headers += configure_file(
  input: vteversion + '.in',
  output: vteversion,
  configuration: vteversion_conf
)

install_headers(
  headers,
  subdir: vte_api_path
)

vtetype = 'vtetypebuiltins'

vtetype_header = gnome.mkenums(
  vtetype + '.h',
  sources: enum_headers,
  h_template: vtetype + '.h.template',
  install_header: true,
  install_dir: vte_pkgincludedir
)
