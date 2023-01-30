This page lists the end-of-support dates for different distributions which we use at the project. Each section should provide a link to the upstream master source, and also, where possible, list the cadence to indicate how often new releases come out

# Linux
## Ubuntu

- POLICY: 6-monthly releases in April and September of each year (previews available ~3 months before), every fourth one (Even numbered years in April) is designated an LTS which has five years of regular support and 10 years of extended (including for community-maintained apps) Non-LTS are supported for nine months
- Upstream details: https://ubuntu.com/about/release-cycle | https://ubuntu.com/kernel/lifecycle
- Show info `cat /etc/lsb-release && uname -r && dpkg -l | grep libc-bin`

Version | Kernel | glibc | release date | end of support | extended support
--- | --- | --- | --- | --- | ---
18.04 | 4.15 | 2.27 | 2018-04 | 2023-03 | 2028-03
20.04 | 5.4 | 2.31 | 2020-04 | 2025-03 | 2030-03
22.04 | 5.15 | 2.35 | 2022-04 | 2027-03 | 2032-03
22.10 | - | 2.35 | 2022-10 | 2023-06 | n/a
23.04 | - | 2.36 | 2023-04 | 2024-12 | n/a

# AIX
- Upstream details https://www.ibm.com/support/pages/aix-support-lifecycle-information
- Show info `oslevel -s` or `oslevel -r` to show current level.
- Table is only showing original levels for each major release and ones we have used or are likely to use:

Version | Release date | End of Support | Next SP
--- | --- | --- | ---
AIX 6.1 TL9 | 2013-11 | 2017-04-30 |
AIX 7.1 TL00 | 2010-09 | 2013-11-30 |
AIX 7.1 TL04 | 2015-12 | 2019-12-31 |
AIX 7.1 TL05 | 2017-10 | 2023-04-30 | 2023-03-10
AIX 7.2 TL0 | 2015-12 | 2018-12-31 |
AIX 7.2 TL4 | 2019-11 | 2022-11-30 |
AIX 7.2 TL5 | 2020-11 | TBC | 2023-04-28
AIX 7.3 TL0 | 2021-12 | 2024-12-31 | 2023-03-10
AIX 7.3 TL1 | 2022-12 | 2025-12-31 | 2023-04-28
