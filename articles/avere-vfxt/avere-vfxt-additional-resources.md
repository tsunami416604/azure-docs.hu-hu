---
title: További fájlokra mutató hivatkozásokat Avere vFXT az Azure-hoz
description: Az Azure-ban Avere vFXT kapcsolatos további információkra mutató hivatkozásokat
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 65e764d843f9e87adee4cf94c1d22b02db80eda0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958834"
---
# <a name="additional-documentation"></a>További dokumentáció

Ez a cikk a Avere Vezérlőpult Felügyeleti felület és a kapcsolódó témakörökben kapcsolatos további dokumentációkra mutató hivatkozásokat tartalmaz. 

## <a name="avere-cluster-documentation"></a>Avere fürt dokumentációja

További Avere fürt dokumentációjában találhatók a webhellyel <http://library.averesystems.com/>. Ezeket a dokumentumokat segítségével megismerheti a fürt képességek és a beállítások konfigurálása. 

* A [FXT fürt létrehozási útmutató](<http://library.averesystems.com/#fxt_cluster>) célja fürtök feladatátvételicsoport-csomópontokon fizikai hardver, de néhány információt a dokumentum, valamint vFXT fürtök esetén fontos. Ezekben a szakaszokban olvasása, új vFXT fürt rendszergazdák is előnyei:
  * [Támogatás és megfigyelési beállítások testreszabása](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>) támogatási feltöltési beállítások testre szabása, és engedélyezze a távoli figyelési ismerteti. 
  * [VServers és globális Namespace konfigurálása](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>) egy ügyfél által használt névtér létrehozásával kapcsolatos információkat tartalmaz.
  * [DNS konfigurálása a Avere fürt](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>) ciklikus időszeletelési DNS konfigurálását ismerteti.
  * [Háttér-tárolóeszközök hozzáadása](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) bemutatja az alapvető kiemelik hozzáadása.

* A [fürt beállítási útmutató](<http://library.averesystems.com/#operations>) teljes hivatkozás, beállítások és a egy Avere fürt lehetőségeket. Egy vFXT fürt használja, ezek a lehetőségek egy részét, de a legtöbb ugyanazokkal a konfigurációs lapokkal érvényesek.

* A [irányítópult útmutató](<http://library.averesystems.com/#operations>) a figyelési funkciók Avere Vezérlőpult fürt használatát ismerteti.

## <a name="vfxt-creation-and-management-documentation"></a>vFXT létrehozásának és kezelésének dokumentációját

A teljes útmutató vfxt.py, a felhő-fürt létrehozása és a felügyeleti segédprogram, a github megadott: [vfxt.py a fürt Felhőfelügyelet](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).  
