vte_xml = 'vte.xml'

vte_conf = configuration_data()
vte_conf.set('VTE_API_VERSION', vte_api_version)
vte_conf.set('VERSION', vte_version)

vte_api_name_in = configure_file(
  input: vte_xml + '.in.in',
  output: vte_api_name + '.xml.in',
  configuration: vte_conf
)

intltool_cache = join_paths(po_dir, '.intltool-merge-cache')

custom_target(
  vte_xml,
  input: vte_api_name_in,
  output: vte_api_name + '.xml',
  command: [find_program('intltool-merge'), '-x', '-u', '-c', intltool_cache, po_dir, '@INPUT@', '@OUTPUT@'],
  build_by_default: true
)
