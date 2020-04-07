---
title: A Power BI-alkalmazások létrehozása – Azure Piactér
description: Ez a cikk a Power BI-alkalmazások Microsoft AppSource-ban való közzétételének magas szintű lépéseit ismerteti. A Power BI-alkalmazás nak is meg kell felelnie ahhoz, hogy közzétehesd a kereskedelmi piacon.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: c348a172b16e12334d33cf2718609694147fdce3
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674526"
---
# <a name="power-bi-app-creation-overview"></a>A Power BI-alkalmazások létrehozása – áttekintés

> [!IMPORTANT]
> Power BI-alkalmazásajánlatainak kezelését áthelyezzük a Cloud Partner Portalról a Partnerközpontba. Az ajánlatok áttelepítéséig kövesse a [Power BI alkalmazásajánlata](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) a Cloud Partner Portal szolgáltatásra vonatkozó utasításait az ajánlatok kezeléséhez.

Ebből a cikkből megtudhatja, hogy miként tehet közzé Power BI-alkalmazásokat a Microsoft [AppSource alkalmazásban.](https://appsource.microsoft.com/) A Power BI-alkalmazások testre szabható tartalmakat csomagolnak, beleértve az adatkészleteket, jelentéseket és irányítópultokat. Ezután az AppSource segítségével használhatja az alkalmazást más Power BI-platformokkal, elvégezheti a fejlesztő által engedélyezett módosításokat és testreszabásokat, és csatlakoztathatja a saját adataihoz.

## <a name="publishing-benefits"></a>Közzétételi előnyök

A kereskedelmi piacon való közzététel előnyei:

- Népszerűsítse vállalatát a Microsoft márkanév használatával.
- Potenciálisan több mint 100 millió Office 365- és Dynamics 365-felhasználót érhet el az AppSource-on, és több mint 200 000 szervezetet az Azure Marketplace-en keresztül.
- Szerezzen kiváló minőségű érdeklődőket ezekről a piacterekről.
- A Microsoft helyszíni és teleértékesítési csapatai népszerűsíthetik a szolgáltatásokat.

## <a name="overview"></a>Áttekintés

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="A Power BI-alkalmazások közzétételének lépései" border="false":::

Ezek a legfontosabb közzétételi lépések:

1. Hozza létre az alkalmazást a Power BI-ban. Kap egy csomagtelepítési linket, amely az ajánlat fő technikai eszköze. Küldje el a tesztcsomagot az előtérbe, mielőtt létrehozna egy ajánlatot a Partnerközpontban. További információt a [Mik azok a Power BI-alkalmazások?](https://docs.microsoft.com/power-bi/service-template-apps-overview)
2. Adja hozzá a marketinganyagokat, például a hivatalos nevet, a leírást és az emblémákat.
3. Adja meg az ajánlat jogi és támogatási dokumentumait, például a használati feltételeket, az adatvédelmi szabályzatot, a támogatási irányelveket és a felhasználói segítséget.
4. Az ajánlat létrehozása: A Partnerközpont segítségével szerkesztheti a részleteket, beleértve az ajánlat leírását, a marketinganyagokat, a jogi információkat, a támogatási információkat és az eszközspecifikációkat.
5. Küldje el közzétételre.
6. Figyelje a folyamatot a Partnerközpontban, ahol az AppSource bevezetési csapata teszteli, ellenőrzi és hitelesíti az alkalmazást.
7. A minősítés t követően tekintse át az alkalmazást a tesztkörnyezetben, és engedje fel. Ez felsorolja azt AppSource (ez "megy él").
8. A Power BI-ban küldje el a csomagot éles környezetbe. További információt [a Power BI alkalmazáskiadás ának kezelése](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release)című témakörben talál.

## <a name="before-you-begin"></a>Előkészületek

Tekintse át az alábbi hivatkozásokat, amelyek sablonokat, tippeket és mintákat tartalmaznak.

- [Power BI-alkalmazás létrehozása](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Tippek a Power BI-alkalmazások használatához](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [Példák](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Követelmények

A kereskedelmi piacon való közzétételhez a Power BI-alkalmazásajánlatnak meg kell felelnie az alábbi műszaki és üzleti követelményeknek.

### <a name="technical-requirements"></a>Technikai követelmények

A legfontosabb technikai eszköz, amire szüksége lesz, egy [Power BI-alkalmazás.](https://go.microsoft.com/fwlink/?linkid=2028636) Ez elsődleges adatkészletek, jelentések vagy irányítópultok gyűjteménye. Opcionális csatlakoztatott szolgáltatásokat és beágyazott adatkészleteket is tartalmaz, amelyeket korábban [tartalomcsomagnak](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction)neveztek. Az ilyen típusú alkalmazások fejlesztéséről a Mik azok a [Power BI-alkalmazások?](https://go.microsoft.com/fwlink/?linkid=2028636)

#### <a name="get-an-installation-web-address"></a>Telepítési webcím beszerezni

Power BI-alkalmazást csak a [Power](https://powerbi.microsoft.com/) BI-környezetben hozhat létre.

1. Jelentkezzen be [Power BI Pro-licenccel.](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)
2. Az alkalmazás létrehozása és tesztelése a Power BI-ban.
3. Amikor megkapja az alkalmazástelepítési webcímet, adja hozzá a **Partnerközpont Műszaki konfiguráció** lapjához.

Miután létrehozta és tesztelte az alkalmazást a Power BI-ban, mentse az alkalmazás telepítési webcímét, mivel szüksége lesz rá [a Power BI-alkalmazásajánlat létrehozásához.](https://aka.ms/AzureCreatePBIServiceApp)

### <a name="business-requirements"></a>Üzleti követelmények

Az üzleti követelmények magukban foglalják az eljárási, szerződéses és jogi kötelezettségeket. A következőket kell tennie:

- Legyen bejegyzett kereskedelmi piackiadó. Ha még nem regisztrált, kövesse a [Legyen kereskedelmi piactér kiadója](https://docs.microsoft.com/azure/marketplace/become-publisher)című részt.
- Olyan tartalom biztosítása, amely megfelel az ajánlat appsource-on való felsorolásának feltételeinek. További információ: [Van egy alkalmazás, amely nek van listázva az AppSource-on? Itt van, hogyan](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Fogadja el és kövesse a [Microsoft adatvédelmi nyilatkozatát.](https://privacy.microsoft.com/privacystatement)

## <a name="next-steps"></a>További lépések

- [Power BI-alkalmazásajánlat létrehozása a Partnerközpontban](https://aka.ms/AzureCreatePBIServiceApp)