---
title: Azure-erőforrások áthelyezése másik régióba
description: Áttekintést nyújt az Azure-erőforrások Azure-régiók közötti áthelyezéséről.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485207"
---
# <a name="moving-azure-resources-across-regions"></a>Az Azure-erőforrások régiók közötti áthelyezése

Ez a cikk az Azure-erőforrások Azure-régiók közötti áthelyezéséről nyújt tájékoztatást.

Az Azure földrajzi területei, régiói és rendelkezésre állási zónái képezik az Azure globális infrastruktúrájának alapját. Az [Azure-földrajzi területek](https://azure.microsoft.com/global-infrastructure/geographies/) általában két vagy több [Azure-régiót tartalmaznak.](https://azure.microsoft.com/global-infrastructure/regions/) A terület egy földrajzi területen belüli terület, amely rendelkezésre állási zónákat és több adatközpontot tartalmaz. 

Miután erőforrásokat telepített egy adott Azure-régióban, számos oka lehet annak, hogy erőforrásokat szeretne áthelyezni egy másik régióba.

- **Igazítás egy régió indításához:** Helyezze át erőforrásait egy újonnan bevezetett Azure-régióba, amely korábban nem volt elérhető.
- **Szolgáltatások/szolgáltatások igazítása**: Erőforrások áthelyezése az adott régióban elérhető szolgáltatások és szolgáltatások előnyeinek kihasználásához.
- **Válasz az üzleti fejlesztésekre:** Erőforrások áthelyezése egy régióba az üzleti változásokra, például egyesülésekre vagy felvásárlásokra reagálva.
- **Igazítás a közelséghez:** Az erőforrásokat áthelyezheti egy helyi régióba a vállalkozásához.
- **Az adatkövetelményeknek való megfelelés:** Erőforrások áthelyezése az adatok tárolási követelményeinek vagy az adatosztályozási igényeknek való megfelelés érdekében. [További információ](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **A központi telepítési követelmények reklisése:** Helyezze át a hibásan üzembe helyezett erőforrásokat, vagy helyezze át a kapacitásigényekre reagálva. 
- **A leszerelésre adott válasz:** Erőforrások áthelyezése a régiók leszerelése miatt.

## <a name="move-process"></a>Folyamat áthelyezése

A tényleges áthelyezési folyamat az áthelyezni kívánt erőforrásoktól függ. Van azonban néhány gyakori kulcsfontosságú lépés:

- **Előfeltételek ellenőrzése:** Az előfeltételek közé tartozik annak biztosítása, hogy a szükséges erőforrások elérhetők legyenek a célrégióban, annak ellenőrzése, hogy elegendő kvótával rendelkezik-e, és annak ellenőrzése, hogy az előfizetés hozzáférhet-e a célrégióhoz.
- **Függőségek elemzése:** Előfordulhat, hogy az erőforrások más erőforrásoktól függenek. Áthelyezés előtt, kitalálni függőségek, hogy az áthelyezett erőforrások továbbra is a várt módon működnek az áthelyezés után.
- **Felkészülés az áthelyezésre:** Ezek azok a lépések, amelyeket az elsődleges régióban az áthelyezés előtt tesz. Előfordulhat például, hogy exportálnia kell egy Azure Resource Manager-sablont, vagy el kell kezdenie az erőforrások replikálását a forrásból a célba.
- **Az erőforrások áthelyezése:** Az erőforrások áthelyezésének módja attól függ, hogy mik azok. Előfordulhat, hogy telepítenie kell egy sablont a célrégióban, vagy át kell adnia az erőforrásokat a célnak.
- **Célerőforrások elvetése:** Az erőforrások áthelyezése után érdemes lehet megtekinteni a célrégióban lévő erőforrásokat, és eldöntheti, hogy van-e valami, amire nincs szüksége.
- **Az áthelyezés**véglegesítése: A célrégió erőforrásainak ellenőrzése után egyes erőforrások végső véglegesítési műveletet igényelhetnek. Például egy célrégióban, amely most az elsődleges régió, előfordulhat, hogy be kell állítania a vész-helyreállítási egy új másodlagos régióban. 
- **Tisztítsa meg a forrást:** Végül, miután minden az új régióban működik, megtisztíthatja és leszerelheti az áthelyezéshez létrehozott erőforrásokat, valamint az elsődleges régióban lévő erőforrásokat.



## <a name="next-steps"></a>További lépések

Az erőforrások régiók közötti áthelyezését támogató témakört az [Erőforrások művelettámogatásának áthelyezése (Áthelyezése) témakörben tetszésben található.](region-move-support.md)
