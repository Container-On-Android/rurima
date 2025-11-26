project(
    'rurima',
    'c',
    meson_version : '>= 0.61',
    version : '0.9.0',
    license : 'MIT',
    default_options : [
        'warning_level=3',
        'b_lto=true',
        'b_lto_mode=thin',
    ],
)

cc = meson.get_compiler('c')
pkgconfig = import('pkgconfig')
pkgconfig_libs = []
libruri_dependencies = []

## Build flags
common_cc_flags = [
    '-Wno-pedantic',
    '-Wno-sign-compare',
    '-Wno-unused-result',
    '-Wno-unused-variable',
    '-Wno-unused-function',
    '-Wno-unused-parameter',
    '-Wno-unused-but-set-variable',
    '-Wno-missing-field-initializers'
]

gcc_cc_flags = [
    '-Wno-restrict',
    '-Wno-format-overflow',
    '-Wno-discarded-qualifiers'
]

clang_cc_flags = [
    '-Wno-newline-eof',
    '-Wno-gnu-zero-variadic-macro-arguments',
    '-Wno-incompatible-pointer-types-discards-qualifiers'
]

add_project_arguments(cc.get_supported_arguments(common_cc_flags), language: 'c')

if cc.get_id() == 'clang'
    add_project_arguments(cc.get_supported_arguments(clang_cc_flags), language: 'c')
else
    add_project_arguments(cc.get_supported_arguments(gcc_cc_flags), language: 'c')
endif

libruri_version = '3.9.3'

prefixdir = get_option('prefix')

# Configuration options.
srcconf = configuration_data()
srcconf.set('_GNU_SOURCE', true)

want_cap = get_option('cap')
want_seccomp = get_option('seccomp')

## Cap.
libcap = dependency('libcap', required: false)
if want_cap
    pkgconfig_libs += libcap
    libruri_dependencies += libcap
    if libcap.found() == false
        add_project_arguments(cc.get_supported_arguments('-DDISABLE_LIBCAP=on'), language: 'c')
    endif
else
    libcap.found() == false
    add_project_arguments(cc.get_supported_arguments('-DDISABLE_LIBCAP=on'), language: 'c')
endif

## Seccomp.
libseccomp = dependency('libseccomp', required: false)
if want_seccomp
    pkgconfig_libs += libseccomp
    libruri_dependencies += libseccomp
    if libseccomp.found() == false
        add_project_arguments(cc.get_supported_arguments('-DDISABLE_LIBSECCOMP=on'), language: 'c')
    endif
else
    libseccomp.found() == false
    add_project_arguments(cc.get_supported_arguments('-DDISABLE_LIBSECCOMP=on'), language: 'c')
endif

# src ruri
subdir('src')

rurima_sources = [
  'src/archive.c',
  'src/checkdep.c',
  'src/dockerhub.c',
  'src/exec.c',
  'src/info.c',
  'src/lxcmirror.c',
  'src/main.c',
  'src/net.c',
  'src/shared.c',
  'src/signal.c',
  'src/subcommand.c',
  'src/easteregg/daijin.c',
]

rurima_deps = [
  libruri_dep,
  libruri_dependencies,
]

executable('rurima',
  rurima_sources,
  dependencies: rurima_deps,
  install: true,
  install_dir: get_option('bindir')
)

install_data('README.md', install_dir: get_option('datadir') / 'doc' / meson.project_name())

summary({
  'Build type':           get_option('buildtype'),
  'Install prefix':       get_option('prefix'),
  'Source directory':     meson.project_source_root(),
  'Build directory':      meson.project_build_root(),
}, section: 'Directories')

summary({
  'libcap':               libcap.found(),
  'libseccomp':           libseccomp.found(),
}, section: 'Dependencies')
