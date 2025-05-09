# SPDX-License-Identifier: LGPL-2.1-or-later

sources = files(
        'timesyncd-conf.c',
        'timesyncd-manager.c',
        'timesyncd-server.c',
)

systemd_timesyncd_sources = files(
        'timesyncd.c',
        'timesyncd-bus.c',
)

timesyncd_gperf_c = custom_target(
        'timesyncd-gperf.c',
        input : 'timesyncd-gperf.gperf',
        output : 'timesyncd-gperf.c',
        command : [gperf, '@INPUT@', '--output-file', '@OUTPUT@'])

generated_sources += timesyncd_gperf_c
sources += timesyncd_gperf_c

if get_option('link-timesyncd-shared')
        timesyncd_link_with = [libshared]
else
        timesyncd_link_with = [libsystemd_static,
                               libshared_static]
endif

libtimesyncd_core = static_library(
        'timesyncd-core',
        sources,
        include_directories : includes,
        dependencies : userspace,
        link_with : timesyncd_link_with,
        build_by_default : false)

executables += [
        libexec_template + {
                'name' : 'systemd-timesyncd',
                'conditions' : ['ENABLE_TIMESYNCD'],
                'sources' : systemd_timesyncd_sources,
                'link_with' : libtimesyncd_core,
                'dependencies' : [
                        libm,
                        threads,
                ],
        },
        libexec_template + {
                'name' : 'systemd-time-wait-sync',
                'conditions' : ['ENABLE_TIMESYNCD'],
                'sources' : files('wait-sync.c'),
                'link_with' : libtimesyncd_core,
        },
        test_template + {
                'sources' : files('test-timesync.c'),
                'link_with' : [
                        libshared,
                        libtimesyncd_core,
                ],
                'dependencies' : libm,
        },
]

custom_target(
        'timesyncd.conf',
        input : 'timesyncd.conf.in',
        output : 'timesyncd.conf',
        command : [jinja2_cmdline, '@INPUT@', '@OUTPUT@'],
        install : conf.get('ENABLE_TIMESYNCD') == 1 and install_sysconfdir_samples,
        install_dir : pkgconfigfiledir)

if conf.get('ENABLE_TIMESYNCD') == 1
        install_data('org.freedesktop.timesync1.conf',
                     install_dir : dbuspolicydir)
        install_data('org.freedesktop.timesync1.service',
                     install_dir : dbussystemservicedir)
        install_data('80-systemd-timesync.list',
                     install_dir : ntpservicelistdir)
        install_data('org.freedesktop.timesync1.policy',
                     install_dir : polkitpolicydir)
endif
