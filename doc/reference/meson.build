private_headers = [
  'config.h',
  'buffer.h',
  'caps.h',
  'debug.h',
  'iso2022.h',
  'keymap.h',
  'marshal.h',
  'matcher.h',
  'ring.h',
  'table.h',
  'trie.h',
  'vteconv.h',
  'vtedraw.h',
  'vteinternal.hh',
  'vterowdata.h',
  'vteseq-list.h',
  'vtestream-base.h',
  'vtestream-file.h',
  'vtestream.h',
  'vtetc.h',
  'vtetree.h',
  'vtetypebuiltins.h',
  'vteunistr.h'
]

version_conf = configuration_data()
version_conf.set('VERSION', vte_version)

version_xml = 'version.xml'

configure_file(
  input: version_xml + '.in',
  output: version_xml,
  configuration: version_conf
)

glib_prefix = glib_dep.get_pkgconfig_variable('prefix')
glib_doc_path = join_paths(glib_prefix, 'share', 'gtk-doc', 'html')

gtk_prefix = gtk_dep.get_pkgconfig_variable('prefix')
gtk_doc_path = join_paths(gtk_prefix, 'share', 'gtk-doc', 'html')

doc_path = join_paths(vte_datadir, 'gtk-doc', 'html', vte_api_name)

gnome.gtkdoc(
  meson.project_name(),
  main_xml: meson.project_name() + '-docs.xml',
  src_dir: [
    top_inc,
    src_inc,
    vte_inc
  ],
  dependencies: libvte_dep,
  scan_args: [
    '--rebuild-types',
    '--ignore-headers=' + ' '.join(private_headers),
    '--deprecated-guards="VTE_DISABLE_DEPRECATED"',
    '--ignore-decorators=_VTE_GNUC_NONNULL\s*\([^)]*\)'
  ],
  mkdb_args: [
    '--source-suffixes=h,hh,c,cc',
    '--xml-mode',
    '--output-format=xml',
    '--name-space=' + meson.project_name()
  ],
  gobject_typesfile: meson.project_name() + '.types',
  fixxref_args: [
    '--extra-dir=@0@'.format(join_paths(glib_doc_path, 'glib')),
    '--extra-dir=@0@'.format(join_paths(glib_doc_path, 'gio')),
    '--extra-dir=@0@'.format(join_paths(gtk_doc_path, 'gdk')),
    '--extra-dir=@0@'.format(join_paths(gtk_doc_path, 'gdk-pixbuf')),
    '--extra-dir=@0@'.format(join_paths(gtk_doc_path, 'gtk'))
  ],
  install: true,
  install_dir: doc_path
)
