# openHAB OSGi-ify

[![GitHub Actions Build Status](https://github.com/openhab/openhab-osgiify/actions/workflows/ci-build.yml/badge.svg?branch=main)](https://github.com/openhab/openhab-osgiify/actions/workflows/ci-build.yml)
[![Jenkins Build Status](https://ci.openhab.org/job/openhab-osgiify/badge/icon)](https://ci.openhab.org/view/All/job/openhab-osgiify/)
[![EPL-2.0](https://img.shields.io/badge/license-EPL%202-green.svg)](https://opensource.org/licenses/EPL-2.0)

This repository contains OSGI-ified versions of dependencies of the openHAB project.

Individual bundles may have different licenses. 
See the NOTICE file in each directory.

> [!NOTE]
> When using the GraalVM collection of bundles, it's important to know that internally it uses ServiceLoader in various locations, which is strictly speaking incompatible with OSGi bundles which each have their own ClassLoader.
> Instead, the OSGi Service Loader Mediator is used to take services published with the Service Provider Interface and publish them as OSGi services so that other bundles can find them.
> Subsequently, the Mediator is also used on the consuming end for the bundles that use ServiceLoader.load to re-write that code on-the-fly to query from the OSGi service registry instead.
> This is managed via additional headers in the osgi.bnd files as appropriate.
> The caveat to this process is that because these bundles aren't truly aware of OSGi's dynamic loading, they just assume that all services will be available when they start up, and can't be notified of (optional) bundles that start afterwards.
> Due to this, any feature that makes use of GraalVM needs to adjust bundle start levels to ensure the approriate dependencies are available in the correct order:
> - Any bundle that provides a language or resources needs to load at start level 78 (before truffle-api)
> - Truffle API needs to load at start level 79 (before polyglot)
> - The rest of the bundles can load at their default start level 80
