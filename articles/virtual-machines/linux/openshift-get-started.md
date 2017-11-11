---
title: "Az Azure áttekintés OpenShift |} Microsoft Docs"
description: "Az Azure-ban OpenShift áttekintése."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 5d154a1c860a5ebd7af2efd55b470bb14efe6c67
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="openshift-in-azure"></a>Az Azure-ban OpenShift

OpenShift egy nyitott és bővíthető tároló platform, amely a vállalat Docker és Kubernetes számos lehetőséget kínál.  

OpenShift Kubernetes tároló vezénylési és felügyeleti tartalmazza. Fejlesztői és műveletek központú eszközök, amelyek lehetővé teszik hozzáadása:

- Gyors alkalmazásfejlesztés.
- Egyszerű telepítés és a méretezésről.
- Hosszú távú a csoportok és alkalmazások életciklusának karbantartási.

OpenShift, amelyek kettő futtatható Azure-ban több verziója is van:

- OpenShift Origin
- OpenShift tárolóplatform
- Online OpenShift
- Dedikált OpenShift

A cikkben szereplő négy verzióinak két érhetők el az ügyfelek számára a saját Azure-ban telepítheti: OpenShift eredete és OpenShift tároló Platform.

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

## <a name="next-steps"></a>Következő lépések

- [A OpenShift közös Előfeltételek konfigurálása az Azure-ban](./openshift-prerequisites.md)
- [Az Azure-ban OpenShift származási telepítése](./openshift-origin.md)
- [Az Azure-ban OpenShift tároló Platform telepítése](./openshift-container-platform.md)
- [Telepítés utáni feladatok](./openshift-post-deployment.md)
- [OpenShift telepítési hibáinak elhárítása](./openshift-troubleshooting.md)
