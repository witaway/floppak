project(
        'floppak', 'c',
        version : '1.0.0',
        meson_version: '>=1.0.0',
        default_options : [
                'warning_level=2',
                'c_std=c17'
        ],
)

cc = meson.get_compiler('c')
add_project_arguments('-D_GNU_SOURCE', language: 'c')

# Keep this in sync with ostree, except remove -Wall (part of Meson
# warning_level 2) and -Werror=declaration-after-statement
add_project_arguments(
        cc.get_supported_arguments([
                '-Werror=shadow',
                '-Werror=empty-body',
                '-Werror=strict-prototypes',
                '-Werror=missing-prototypes',
                '-Werror=implicit-function-declaration',
                '-Werror=pointer-arith',
                '-Werror=init-self',
                '-Werror=missing-declarations',
                '-Werror=return-type',
                '-Werror=overflow',
                '-Werror=int-conversion',
                '-Werror=parenthesis',
                '-Werror=incompatible-pointer-types',
                '-Werror=misleading-indentation',
                '-Werror=missing-include-dirs',
                '-Werror=aggregate-return',
                '-Werror=switch-default',
                '-Wswitch-enum',

                # Deliberately not warning about these, ability to zero-initialize
                # a struct is a feature
                '-Wno-missing-field-initializers',
                '-Wno-error=missing-field-initializers',
        ]),
        language : 'c',
)

if (
        cc.has_argument('-Werror=format=2')
        and cc.has_argument('-Werror=format-security')
        and cc.has_argument('-Werror=format-nonliteral')
)
        add_project_arguments([
              '-Werror=format=2',
              '-Werror=format-security',
              '-Werror=format-nonliteral',
        ], language : 'c')
endif

cdata = configuration_data()
cdata.set_quoted(
        'PACKAGE_STRING',
        '@0@ @1@'.format(meson.project_name(), meson.project_version())
)

configure_file(
        output: 'config.h',
        configuration: cdata
)

if get_option('installdir') != ''
        install_dir = get_option('installdir')
elif meson.is_subproject()
        install_dir = get_option('libexecdir')
else
        install_dir = get_option('bindir')
endif

sources = [
        'src/main.c',
        'src/runner/data-structures/destinations-graph.h',
        'src/runner/data-structures/destinations-graph-ops.c',
        'src/runner/data-structures/destinations-graph-ops-linked-list.c',
        'src/runner/data-structures/destinations-graph-ops-node.c',
        'src/utils.c',
        'src/utils.h',
        'src/runner/runner.h',
        'src/runner/runner.c',
        'src/runner/parse-mountinfo.h',
        'src/runner/bind-mount.c',
        'src/runner/parse-mountinfo.c',
]

flatpak = subproject('flatpak')

floppak = executable(
        'floppak',
        sources,
        install : true,
        install_dir: install_dir,
        install_rpath: get_option('install_rpath'),
        dependencies: [
                dependency('glib-2.0', version : '>=2.46')
        ]
)

test('test', floppak)