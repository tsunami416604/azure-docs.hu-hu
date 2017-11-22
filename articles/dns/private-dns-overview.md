---
title: "A privát tartományok Azure DNS-sel |} Microsoft Docs"
description: "A Microsoft Azure szolgáltatást tartalmazó titkos DNS áttekintése."
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 95cf8ab2bd34e698e12452e062687219bad49eb6
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2017
---
# <a name="using-azure-dns-for-private-domains"></a>A privát tartományok Azure DNS-sel
A tartománynévrendszer, vagy a DNS-, felelős fordítása (vagy feloldása) a szolgáltatás nevét az IP-címét. Az Azure DNS egy olyan üzemeltetési szolgáltatás DNS-tartományok, biztosítani a névfeloldást a Microsoft Azure-infrastruktúra használatával.  Mellett az internetre irányuló DNS-tartományok Azure DNS-ben mostantól is támogatja a saját DNS-tartományok, előzetes verziójú funkciók.  
 
Az Azure DNS biztosít egy megbízható, biztonságos DNS-szolgáltatás kezeléséhez, és nem kell egyéni DNS megoldás hozzáadása egy virtuális hálózati tartomány neveinek feloldásához. Saját DNS-zónák történő ma használható Azure által biztosított neve helyett a saját egyéni tartománynevek használatát teszi lehetővé.  Egyéni tartománynevek használatával segítségével testre szabni a virtuális hálózati architektúrát a legjobban megfeleljen a szervezet igényeinek. Névfeloldás biztosít a virtuális gépek a virtuális hálózaton belül, és virtuális hálózatok között. Emellett zónák neve konfigurálható egy vegyes-horizon nézet – egy olyan magánhálózat és a nyilvános DNS-zóna neve azonos.

![DNS áttekintése](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>Előnyök

* **Eltávolítja az egyéni DNS-megoldások szükségességét.** Korábban számos ügyfél létrehozott virtuális hálózatban lévő DNS-zónák kezelése egyéni DNS-megoldásokat.  DNS-zóna kezelési most végezhető Azure natív infrastruktúrája, amely eltávolítja a okozta terheket létrehozása és kezelése az egyéni DNS-megoldásokat használ.

* **Használni közös DNS rekord típusát.**  Az Azure DNS támogatja A, AAAA, CNAME, MX, NS, PTR, SOA, SRV és TXT rekord.

* **Automatikus állomásnév rekord kezelése.** Az egyéni DNS-rekordokat tartalmazó, valamint Azure automatikusan is kezeli állomásnév rögzíti a megadott virtuális hálózatokon lévő virtuális géphez.  Ez lehetővé teszi, hogy optimalizálja a tartománynevek használatával anélkül egyéni DNS-megoldások létrehozásához, vagy módosítsa az alkalmazás.

* **Állomásnév-feloldási virtuális hálózatok között.** Azure által biztosított állomásnevek, eltérően saját DNS-zónák virtuális hálózatok között megosztható legyen.  Ez a funkció egyszerűbbé teszi a kereszt-hálózat és a szolgáltatás felderítése forgatókönyvek például a virtuális hálózati társviszony-létesítés.

* **Jól ismert eszközökkel és a felhasználói élményre.** A tanulási görbére csökkentése érdekében az új ajánlat által használt a már neves Azure DNS-eszközök (PowerShell Resource Manager-sablonok, a REST API-t).

* **Vegyes-horizon DNS-támogatás.** Az Azure DNS zónák létrehozása ezzel a névvel, a különböző választ a virtuális hálózaton belül, és a nyilvános internetről feloldható teszi lehetővé.  A felosztott-horizon DNS jellemző forgatókönyv, hogy adja meg a virtuális hálózaton belüli használatra szolgáltatás dedikált verzióját.


## <a name="pricing"></a>Díjszabás

A felügyelt előzetes saját DNS-zónák az ingyenesen elérhető. Általános rendelkezésre állás érdekében során ez a szolgáltatás a használat alapú árképzési modellt a meglévő Azure DNS-kínál hasonló fogja használni. 


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozzon létre egy saját DNS-zóna](./private-dns-getstarted-powershell.md) Azure DNS-ben.

További információk a DNS-zónák és rekordok ellátogatva: [DNS-zónák és áttekintése rögzíti](dns-zones-records.md).

Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.

