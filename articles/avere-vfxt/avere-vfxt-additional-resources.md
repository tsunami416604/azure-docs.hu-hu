---
title: További fájlokra mutató hivatkozásokat Avere vFXT az Azure-hoz
description: Az Azure-ban Avere vFXT kapcsolatos további információkra mutató hivatkozásokat
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: v-erkell
ms.openlocfilehash: 2efbe7ddc39b8bde76ee4a135f3f44af0864a374
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188572"
---
# <a name="additional-documentation"></a>További dokumentáció

Ez a cikk a Avere Vezérlőpult Felügyeleti felület és a kapcsolódó témakörökben kapcsolatos további dokumentációkra mutató hivatkozásokat tartalmaz. 

## <a name="avere-cluster-documentation"></a>Avere fürt dokumentációja

További Avere fürt dokumentációjában találhatók a webhellyel <https://azure.github.io/Avere/>. Ezeket a dokumentumokat segítségével megismerheti a fürt képességek és a beállítások konfigurálása. 

* A [FXT fürt létrehozási útmutató](<https://azure.github.io/Avere/#fxt_cluster>) célja fürtök feladatátvételicsoport-csomópontokon fizikai hardver, de néhány információt a dokumentum, valamint vFXT fürtök esetén fontos. Ezekben a szakaszokban olvasása, új vFXT fürt rendszergazdák is előnyei:
  * [Támogatás és megfigyelési beállítások testreszabása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) támogatási feltöltési beállítások testre szabása, és engedélyezze a távoli figyelési ismerteti. 
  * [VServers és globális Namespace konfigurálása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) egy ügyfél által használt névtér létrehozásával kapcsolatos információkat tartalmaz.
  * [DNS konfigurálása a Avere fürt](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) ciklikus időszeletelési DNS konfigurálását ismerteti.
  * [Háttér-tárolóeszközök hozzáadása](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) bemutatja az alapvető kiemelik hozzáadása.

* A [fürt beállítási útmutató](<https://azure.github.io/Avere/#operations>) teljes hivatkozás, beállítások és a egy Avere fürt lehetőségeket. Egy vFXT fürt használja, ezek a lehetőségek egy részét, de a legtöbb ugyanazokkal a konfigurációs lapokkal érvényesek.

* A [irányítópult útmutató](<https://azure.github.io/Avere/#operations>) a figyelési funkciók Avere Vezérlőpult fürt használatát ismerteti.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT létrehozásának és kezelésének dokumentációját

A teljes útmutató vfxt.py, a felhő-fürt létrehozása és a felügyeleti segédprogram, a githubon áll rendelkezésre: [A felhő vfxt.py-kezelő](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
