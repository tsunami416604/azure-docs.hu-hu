---
title: Mi az Azure-erőforrás-mozgató?
description: További tudnivalók az Azure-beli erőforrás-átköltöztetésről
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 621c5e7b0061ccd76fd0109552107915b943511f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653595"
---
# <a name="what-is-azure-resource-mover"></a>Mi az Azure-erőforrás-mozgató?

Ez a cikk áttekintést nyújt az Azure Resource mozgató szolgáltatásról. Az erőforrás-mozgató segítségével áthelyezheti az Azure-erőforrásokat az Azure-régiók között.

Az erőforrásokat különböző Azure-régiókba helyezheti át:

- **Igazítás régióhoz – indítás**: erőforrások áthelyezése egy újonnan bevezetett Azure-régióba, amely korábban nem volt elérhető.
- **Szolgáltatások/szolgáltatások igazítása**: az erőforrások áthelyezésével kihasználhatja az adott régióban elérhető szolgáltatások és szolgáltatások előnyeit.
- **Válaszadás az üzleti fejleményekre**: erőforrások áthelyezése egy régióba az üzleti változások, például az összeolvadások vagy a beszerzések megválaszolására válaszul.
- **Közelség**: erőforrások áthelyezése a vállalat helyi régiójába.
- A **követelmények teljesítése**: helyezze át az erőforrásokat az adattárolási követelményekhez való igazításhoz vagy az adatbesorolási igényekhez.
- **Válaszadás a központi telepítési követelményekre**: a hiba miatt üzembe helyezett erőforrások áthelyezése vagy a kapacitási igényekre való áttérés.
- **Válasz a leszerelésre**: erőforrások áthelyezése, mert egy régiót leszereltek.

> [!IMPORTANT]
> Az Azure-beli erőforrás-mozgató szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.

## <a name="why-use-resource-mover"></a>Miért érdemes az erőforrás-Mozgatót használni?

Az erőforrás-mozgató a következőket biztosítja:

- Egyetlen központ az erőforrások régiók közötti áthelyezéséhez.
- Csökkentheti a mozgatási időt és a bonyolultságot. Minden, amire szüksége van, egyetlen helyen kell lennie.
- Egyszerű és egységes felület a különböző típusú Azure-erőforrások áthelyezéséhez.
- Egyszerű módszer az áthelyezni kívánt erőforrások függőségeinek azonosítására. Ezzel a művelettel a kapcsolódó erőforrásokat együtt helyezheti át, így minden a várt módon működik a megcélzott régióban, az áthelyezést követően.
- A forrás régió erőforrásainak automatikus törlése, ha az áthelyezés után törölni szeretné őket.
- Tesztelés: Kipróbálhatja a mozgatást, majd elvetheti, ha nem szeretne teljes áthelyezést végezni.

## <a name="move-across-regions"></a>Áthelyezés régiók között

Az erőforrások régiók közötti áthelyezéséhez válassza ki az áthelyezni kívánt erőforrásokat. Az erőforrás-mozgató ellenőrzi ezeket az erőforrásokat, és feloldja a más erőforrásokra vonatkozó függőségeket. Ha függőségek vannak, néhány lehetőség közül választhat:
- Helyezze át a függő erőforrásokat a célként megadott régióba.
- Ne helyezze át a függő erőforrásokat, de használja inkább az egyenértékű erőforrásokat a célként megadott régióban.

Az összes függőség feloldása után az erőforrás-mozgató végigvezeti egy egyszerű áthelyezési folyamaton.

1. Elindítja a kezdeti áthelyezést.
2. A kezdeti áthelyezés után eldöntheti, hogy véglegesíti és befejezi az áthelyezést, vagy elveti az áthelyezést.
3. A lépés elvégzése után eldöntheti, hogy törölni kívánja-e a forrás helyének erőforrásait.

Az erőforrásokat áthelyezheti az erőforrás-mozgató központ régiói között, vagy egy erőforráscsoporthoz is. [További információ](select-move-tool.md)

## <a name="what-resources-can-i-move-across-regions"></a>Milyen erőforrásokat lehet áthelyezni a régiók között?

Az erőforrás-mozgató használatával jelenleg a következő erőforrásokat helyezheti át a régiók között:

- Azure-beli virtuális gépek és kapcsolódó lemezek
- Hálózati adapterek (NIC-k)
- Rendelkezésre állási csoportok 
- Azure-beli virtuális hálózatok 
- Nyilvános IP-címek
- Hálózati biztonsági csoportok (NSG-k)
- Belső és nyilvános terheléselosztó 
- Azure SQL Database-adatbázisok és rugalmas készletek


## <a name="next-steps"></a>Következő lépések

[További](about-move-process.md) információ az erőforrás-mozgató összetevőkről és az áthelyezési folyamatról.
