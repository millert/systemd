# SPDX-License-Identifier: LGPL-2.1-or-later

if conf.get('ENABLE_ENVIRONMENT_D') != 1
        subdir_done()
endif

executables += [
        executable_template + {
                'name' : '30-systemd-environment-d-generator',
                'sources' : files('environment-d-generator.c'),
                'install_dir' : userenvgeneratordir,
        },
]

install_emptydir(environmentdir)
# install_symlink() does not support broken symlink, and /etc/environment is not managed by us, thus
# may not exist. Hence, we need to create it by using the custom install script here.
# See https://github.com/mesonbuild/meson/pull/12266 .
meson.add_install_script(sh, '-c',
                         ln_s.format(sysconfdir / 'environment',
                                     environmentdir / '99-environment.conf'))
