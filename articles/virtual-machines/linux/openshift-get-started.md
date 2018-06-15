---
title: Az Azure áttekintés OpenShift |} Microsoft Docs
description: Az Azure-ban OpenShift áttekintése.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: c8e740a66271c88b3abb036867d1760cc9e77607
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944501"
---
# <a name="openshift-in-azure"></a>Az Azure-ban OpenShift

OpenShift egy nyitott és bővíthető tároló platform, amely a vállalat Docker és Kubernetes számos lehetőséget kínál.  

OpenShift Kubernetes tároló vezénylési és felügyeleti tartalmazza. Fejlesztői és műveletek központú eszközök, amelyek lehetővé teszik hozzáadása:

- Gyors alkalmazásfejlesztés.
- Egyszerű telepítés és a méretezésről.
- Hosszú távú a csoportok és alkalmazások életciklusának karbantartási.

Több verzió OpenShift érhetők el:

- OpenShift Origin
- OpenShift tárolóplatform
- Online OpenShift
- Dedikált OpenShift

A négy verziók cikkben szereplő, csak két érhető el, hogy az ügyfelek központi telepítése az Azure-ban: OpenShift eredete és OpenShift tároló Platform.

## <a name="openshift-origin"></a>OpenShift Origin

Forrása: egy [nyílt forráskódú](https://www.openshift.org/) , amely támogatja a közösségi OpenShift a felsőbb rétegbeli projekt. Forrás CentOS vagy a Red Hat Enterprise Linux (RHEL) telepíthető.

## <a name="openshift-container-platform"></a>OpenShift tárolóplatform

Tároló Platform egy nagyvállalati használatra kész [kereskedelmi verzió](https://www.openshift.com) a Red Hat támogatja. Ebben a verzióban a felhasználók a szükséges jogosultságok OpenShift tároló platform beszerzési, telepítés és a teljes infrastruktúra kezelése felelős.

Mert az ügyfelek "tulajdonosa" a teljes platform, telepíthetik azt a helyszíni adatközpontját, illetve a nyilvános felhők (például az Azure, az AWS vagy a Google).

## <a name="openshift-online"></a>Online OpenShift

A Red Hat felügyelt online van *több-bérlős* tároló Platform használó OpenShift. Red Hat kezeli az összes, az alkalmazás mögötti infrastruktúra (például virtuális gépek, OpenShift fürt, a hálózat és tárolás). 

Ebben a verzióban az ügyfél telepíti a tárolókat, de nem szabályozza, a tárolók futtassa mely állomásokat keresztül. Mivel Online több-bérlős, az ugyanazon Virtuálisgép-gazdák más felhasználóktól tárolóként tárolók is található. Költség tároló működik.

## <a name="openshift-dedicated"></a>Dedikált OpenShift

A Red Hat felügyelt dedikált van *single-bérlő* tároló Platform használó OpenShift. Red Hat kezeli az összes, az alkalmazás mögötti infrastruktúra (virtuális gépek, OpenShift fürt hálózati, tárolási, stb.). A fürt egy ügyfél és a nyilvános felhőben (például az AWS vagy a Google, az Azure-ral korai 2018 várható). A kiindulási fürt négy alkalmazás csomópontok 48,000 $ (előre fizetett) évente magában foglalja.

## <a name="next-steps"></a>További lépések

- [A OpenShift közös Előfeltételek konfigurálása az Azure-ban](./openshift-prerequisites.md)
- [Az Azure-ban OpenShift származási telepítése](./openshift-origin.md)
- [Az Azure-ban OpenShift tároló Platform telepítése](./openshift-container-platform.md)
- [Telepítés utáni feladatok](./openshift-post-deployment.md)
- [OpenShift telepítési hibáinak elhárítása](./openshift-troubleshooting.md)
