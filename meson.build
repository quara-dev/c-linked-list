project('c-linked-list',
	['c'],
	default_options : [
		'warning_level=3',
		'werror=false',
		# This project defaults to a release build
		'debug=false',
		'optimization=2',
		# `build.*` options affect `native: true targets`
		# plain options affect `native: false` targets.
		'c_std=c17', 'build.c_std=c17',
	],
	meson_version: '>=0.58.0',
	version: '1.0'
)

################################
# Project Options and Settings #
################################

# Pick up our common compiler variables + desired_*_flags variables
subdir('meson/compiler')
subdir('meson/compiler/c')

if get_option('debug')
	add_project_arguments('-DDEBUG',
		language: ['c'])
	add_project_arguments('-DDEBUG',
		language: ['c'], native: true)
endif

if get_option('disable-builtins')
	desired_common_compile_flags += '-fno-builtin'
endif

if get_option('disable-stack-protection')
	desired_common_compile_flags += '-fno-stack-protector'
endif

if get_option('enable-pedantic')
	desired_common_compile_flags += '-pedantic'
else
	desired_common_compile_flags += '-Wno-pedantic'
endif

if get_option('enable-pedantic-error')
	desired_common_compile_flags += '-pedantic-error'
endif

enable_testing = get_option('enable-testing')

compile_settings_list = [
	{'lang': 'c', 'compiler': host_c_compiler, 'flags': desired_c_compile_flags, 'isnative': false},
	{'lang': 'c', 'compiler': native_c_compiler, 'flags': desired_native_c_compile_flags, 'isnative': true},
]

link_settings_list = [
	{'lang': 'c', 'compiler': host_c_compiler, 'flags': [], 'isnative': false},
	{'lang': 'c', 'compiler': native_c_compiler, 'flags': [], 'isnative': true},
]

# Process the compilation flags
subdir('meson/compiler/check-and-apply-flags')

#################
# Build Modules #
#################

if enable_testing
    subdir('build/test/cmocka')
endif

#######################
# Process Source Tree #
#######################

# Add files to this variable if you want them analyzed by clang-tidy
clangtidy_files = []
subdir('src')

# Tests currently not implemented (issue #1)
# subdir('test')

###################
# Tooling Modules #
###################

subdir('meson/analysis/clang-tidy')
subdir('meson/analysis/complexity')
subdir('meson/analysis/cppcheck')
subdir('meson/analysis/sloccount')
subdir('meson/analysis/vale')
subdir('meson/format')
subdir('meson/docs/doxygen')

#############
# Packaging #
#############

# These macros allow you to simplify the declaration of includes for common
# include paths. Examples:
#	build_root_include.format('doc'),
#	app_include.format('APP')
build_root_include = meson.project_build_root() + ':@0@'
app_include = meson.project_build_root() / 'src/app:@0@'
lib_include = meson.project_build_root() / 'src/lib:@0@'

host_pkg_files = [
	build_root_include.format('docs'),
	app_include.format('APP')
]

native_pkg_files = [
	build_root_include.format('docs'),
	app_include.format('APP_native')
]

# Invoke the package module
subdir('meson/package')
