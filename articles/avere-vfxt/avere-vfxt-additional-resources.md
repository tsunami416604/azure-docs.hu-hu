---
title: További linkek az Avere vFXT for Azure-ról
description: Az Azure-hoz való Avere vFXT szolgáltatással kapcsolatos további információkra mutató hivatkozások
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78e0500057baf407f1d26afe29d9df1e525cbf3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153768"
---
# <a name="additional-documentation"></a>További dokumentáció

Ez a cikk az Avere Vezérlőpult kezelőfelületével és a kapcsolódó témakörökkel kapcsolatos további dokumentációra mutató hivatkozásokat tartalmaz.

## <a name="avere-cluster-documentation"></a>Avere fürt dokumentáció

Az Avere fürt további dokumentációja <https://azure.github.io/Avere/>megtalálható a webhelyen: . Ezek a dokumentumok segítenek megérteni a fürt képességeit és beállításainak konfigurálását.

* Az [FXT-fürtlétrehozási útmutató](<https://azure.github.io/Avere/#fxt_cluster>) fizikai hardvercsomópontokból álló fürtökhöz készült, de a dokumentumban szereplő egyes információk a vFXT-fürtök esetében is relevánsak. Az új vFXT-fürtrendszergazdái nak különösen az alábbi szakaszok olvasása előnyeit élvezhetik:
  * [A Támogatási és figyelési beállítások testreszabása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) a támogatási feltöltési beállítások testreszabását és a távoli figyelés engedélyezését ismerteti.
  * [A VServers és a globális névtér konfigurálása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) az ügyfélfelé néző névtér létrehozásáról tartalmaz információkat.
  * [A DNS konfigurálása az Avere fürthöz](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) a ciklikus multiplexeléses DNS konfigurálása.
  * [Háttérrendszerű tárolási](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) dokumentumok hozzáadása alapvető fájlkezelők hozzáadásához.

* A [fürtkonfigurációs útmutató](<https://azure.github.io/Avere/#operations>) az Avere-fürt beállításainak és beállításainak teljes hivatkozása. A vFXT-fürt a beállítások egy részét használja, de ugyanazon konfigurációs lapok többsége érvényes.

* Az [Irányítópult-útmutató](<https://azure.github.io/Avere/#operations>) bemutatja, hogyan használható az Avere vezérlőpult fürtfigyelő szolgáltatásai.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT létrehozási és kezelési dokumentáció

A Vfxt.py, egy parancsfájlalapú felhőfürt-létrehozási és -felügyeleti segédprogram használatának teljes útmutatója a GitHubon található: [A felhőalapú fürtkezelés vfxt.py.](https://github.com/Azure/AvereSDK/blob/master/docs/README.md)
