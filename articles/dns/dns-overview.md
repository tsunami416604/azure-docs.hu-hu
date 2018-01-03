---
title: "Az Azure DNS áttekintése |} Microsoft Docs"
description: "A Microsoft Azure szolgáltatást tartalmazó DNS áttekintése. A Microsoft Azure-tartomány üzemeltetésére."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: f255fd9621ff90bfbb3ad193faa64495acf7ecd7
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="azure-dns-overview"></a>Az Azure DNS áttekintése

A tartománynévrendszer, vagy a DNS-, felelős fordítása (vagy feloldása) az IP-címét egy webhely vagy szolgáltatás neve. Az Azure DNS egy olyan üzemeltetési szolgáltatás DNS-tartományok, biztosítani a névfeloldást a Microsoft Azure-infrastruktúra használatával. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. Az Azure DNS most is támogatja a saját DNS-tartományok. További információkért lásd: [titkos tartományok Azure DNS használatával](private-dns-overview.md).

![DNS áttekintése](./media/dns-overview/scenario.png)

## <a name="features"></a>Szolgáltatások

* **Megbízhatóság és teljesítmény** -DNS-tartományok Azure DNS-ben üzemeltetett globális hálózata Azure DNS névkiszolgálóit. Az Azure DNS használja, nem egyedi a hálózat, hogy minden DNS-lekérdezést a legközelebbi elérhető DNS-kiszolgáló válaszolt. Gyors teljesítmény és a magas rendelkezésre állást biztosít a tartományhoz.

* **Zökkenőmentes integráció** – az Azure DNS-szolgáltatás segítségével az Azure-szolgáltatások DNS-rekordok kezelése, és adja meg a DNS, valamint a külső erőforrásokhoz is használható. Az Azure DNS integrálva van az Azure portálon, és a hitelesítő adatokkal, számlázási és támogatási szerződése használja, mint a más Azure-szolgáltatásokkal.

* **Biztonsági** – az Azure DNS szolgáltatást az Azure Resource Manager alapul. Ilyen előnyöket az erőforrás-kezelő szolgáltatásait, például a szerepköralapú hozzáférés-vezérlés, a vizsgálati naplók és a erőforrás zárolását. A tartományok és a rekordok az Azure portál, Azure PowerShell-parancsmagok és a platformok közötti Azure CLI segítségével is kezelhető. Automatikus DNS-kezelési igénylő alkalmazásokhoz integrálható a REST API-t és az SDK szolgáltatás.

Az Azure DNS jelenleg nem támogatja tartománynevek megvásárlását. Tartományok beszerezni kívánt, ha meg szeretné használni, a külső tartományregisztráló nevét. A regisztráló általában egy kis éves díj költségek. A tartomány DNS-rekordok Management majd az Azure DNS-lehet üzemeltetni. Lásd: [az Azure DNS-tartomány delegálása az](dns-domain-delegation.md) részleteiről.

## <a name="pricing"></a>Díjszabás

DNS számlázási az Azure-ban és a DNS-lekérdezések száma által üzemeltetett DNS-zónák számát alapul. További információt a díjszabás látogasson el [Azure DNS árképzési](https://azure.microsoft.com/pricing/details/dns/).

## <a name="faq"></a>GYIK

További Azure DNS szolgáltatással kapcsolatos gyakran ismételt kérdések: a [Azure DNS gyakran ismételt kérdések](dns-faq.md).

## <a name="next-steps"></a>További lépések

További információk a DNS-zónák és rekordok ellátogatva: [DNS-zónák és áttekintése rögzíti](dns-zones-records.md).

Megtudhatja, hogyan [hozzon létre egy DNS-zóna](./dns-getstarted-create-dnszone-portal.md) Azure DNS-ben.

Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.

