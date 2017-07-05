gir_incs = [
  'Gdk-' + gtk_api_version,
  'Gtk-' + gtk_api_version,
  'Pango-1.0'
]

gir_extra_args = [
  '-DVTE_COMPILATION',
  '--c-include=vte/vte.h'
]

gir_dir = join_paths(vte_datadir, 'gir-' + vte_gir_version)
typelib_dir = join_paths(vte_libdir, 'girepository-' + vte_gir_version)

libvte_gir = gnome.generate_gir(
  libvte,
  sources: headers + vtetype_enums + gir_sources,
  nsversion: vte_api_version,
  namespace: vte_gir_ns,
  export_packages: vte_api_name,
  includes: gir_incs,
  dependencies: libvte_dep,
  include_directories: vte_inc,
  install: true,
  install_dir_gir: gir_dir,
  install_dir_typelib: typelib_dir,
  extra_args: gir_extra_args
)
