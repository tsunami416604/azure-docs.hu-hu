---
title: További hivatkozások az Azure-hoz készült avere-vFXT
description: Az Azure-hoz készült avere-vFXT kapcsolatos további információkra mutató hivatkozások
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: rohogue
ms.openlocfilehash: c8ac08e3d03e8eb525cad7d73bece40c515e31a1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256293"
---
# <a name="additional-documentation"></a>További dokumentáció

Ez a cikk a avere Vezérlőpult felügyeleti felületével és a kapcsolódó témakörökkel kapcsolatos további dokumentációra mutató hivatkozásokat tartalmaz. 

## <a name="avere-cluster-documentation"></a>Avere-fürt dokumentációja

További avere-fürt dokumentációja a webhelyén, a következő címen érhető el: <https://azure.github.io/Avere/>. Ezek a dokumentumok segítenek megérteni a fürt képességeit, valamint a beállítások konfigurálásának módját. 

* A [FXT-fürt létrehozási útmutatója](<https://azure.github.io/Avere/#fxt_cluster>) olyan fürtökhöz lett tervezve, amelyek fizikai hardveres csomópontokból állnak, de a dokumentumban található információk a vFXT-fürtökhöz is relevánsak. Az új vFXT-fürt rendszergazdái az alábbi fejezetek elolvasását élvezhetik:
  * A [támogatási és figyelési beállítások testreszabása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) elmagyarázza, hogyan szabhatja testre a támogatás feltöltési beállításait és engedélyezheti a távoli figyelést. 
  * A [VServers és a globális névtér konfigurálása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) az ügyfélre irányuló névtér létrehozásával kapcsolatos információkat tartalmaz.
  * [A DNS konfigurálása a AVERE-fürthöz](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) elmagyarázza, hogyan kell konfigurálni a ciklikus multiplexelés DNS-t.
  * [Háttérbeli tárolási dokumentumok hozzáadása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) a Core filers hozzáadásához.

* A [fürt konfigurációs útmutatója](<https://azure.github.io/Avere/#operations>) egy avere-fürt beállításainak és beállításainak teljes referenciája. A vFXT-fürtök ezen beállítások egy részhalmazát használják, de a legtöbb esetben ugyanazok a konfigurációs lapok érvényesek.

* Az [irányítópult-útmutató](<https://azure.github.io/Avere/#operations>) ismerteti, hogyan használható a avere Vezérlőpult fürt figyelési funkciói.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT-létrehozási és-kezelési dokumentáció

A vfxt.py, a felhőalapú fürt létrehozási és felügyeleti segédprogramjának használatával kapcsolatos teljes útmutató a GitHubon érhető el: a vfxt.py-alapú [felhőalapú fürt kezelése](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
