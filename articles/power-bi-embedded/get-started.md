---
title: Ismerkedés a Microsoft Power BI Embedded szolgáltatással | Microsoft Docs
description: A Power BI Embedded és az üzletiintelligencia-alkalmazások
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/11/2018
ms.author: maghan
ms.openlocfilehash: 33d948ee283ff3ee873e2f17ee07a7a889889925
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969493"
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>A Mcirosoft Power BI Embedded bemutatása

A **Power BI Embedded** egy gyors módszert kínál a független szoftverszállítók (ISV-k) és fejlesztők számára a látványos vizualizációk, jelentések és irányítópultok hozzáadására az alkalmazásokhoz egy kapacitásalapú, óradíjas modellben.

![Beágyazási folyamat ábrája](media/get-started/introduction.png)

A Power BI Embedded az ISV-k, azok fejlesztői és az ügyfelek számára is számtalan előnyt kínál, az ISV például ingyen kezdhet vizualizációkat készíteni a Power BI Desktoppal. Az ISV-k gyorsabban juttathatják termékeiket piacra, ha csökkenthetik a vizuális elemzési fejlesztésekre fordított erőfeszítéseket, és kiemelkedhetnek versenytársaik közül egyéni adatkezelési élményt kínáló termékeikkel. Akár úgy is dönthetnek, hogy a beágyazott elemzések által teremtett többletértéket prémium szolgáltatásként árusítják.

A fejlesztők idejüket az alkalmazás fő képességeinek kialakítására fordíthatják, és nem kell annyit foglalkozniuk a vizualizációk és elemzések kifejlesztésével. Gyorsan reagálhatnak az ügyfelek jelentésekkel és irányítópultokkal kapcsolatos igényeire, és az új megoldásokat könnyedén beágyazhatják a teljes körű dokumentációval rendelkező API-k és SDK-k használatával. Azzal, hogy engedélyezik a könnyen navigálható adatfelderítési képességeket alkalmazásaikban, az ISV-k lehetővé tehetik ügyfeleik számára, hogy gyors, adatokon alapuló, az adott környezethez igazodó és megbízható döntéseket hozhassanak bármilyen eszközön.

## <a name="register-an-application-within-azure-active-directory"></a>Alkalmazás regisztrálása az Azure Active Directory szolgáltatásban

Az egyéni alkalmazásokba történő beágyazáshoz az Azure Active Directory (AAD) szolgáltatásban regisztrált alkalmazásra van szükség. A regisztrált alkalmazás használatához a bérlőnek Power BI-bérlőnek kell lennie. A Power BI-bérlő azt jelenti, hogy a cég vagy szervezet legalább egy felhasználója előfizetett a Power BI használatára. Ha egy felhasználó regisztrált a Power BI-ra, a Power BI API-k megjelennek a regisztrált alkalmazásban.

Az alkalmazások AAD-regisztrálásával kapcsolatos további információkért lásd az [Azure AD alkalmazás Power BI-tartalmak beágyazásához történő regisztrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/) foglalkozó cikket.

## <a name="embed-content-in-your-application"></a>Tartalmak beágyazása az alkalmazásba

Miután regisztrált egy alkalmazást az AAD-ben, ágyazza bele a Power BI-tartalmakat. A tartalmak beágyazásához használja a REST és a JavaScript API-kat.

Az első lépések megtételét különböző minták segítik, lásd az [irányítópult, csempe vagy jelentés alkalmazásba történő beágyazását](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/) bemutató cikket.

## <a name="get-capacity-and-move-to-production"></a>Kapacitás kiépítése és áthelyezése éles környezetbe

Hozzon létre Power BI Embedded-kapacitásokat a Microsoft Azure-ban, hogy az alkalmazást áthelyezhesse az éles környezetbe. A kapacitások létrehozásával kapcsolatos információkért lásd a [Power BI Embedded-kapacitások Azure Portalon történő létrehozásával](create-capacity.md) foglalkozó témakört.

> [!IMPORTANT]
> Mivel a beágyazási jogkivonatok csak fejlesztői tesztelésre szolgálnak, a Power BI-főfiókok által létrehozható beágyazási jogkivonatok száma korlátozott. Beágyazási forgatókönyvek létrehozásához [kapacitást kell vásárolni](https://docs.microsoft.com/power-bi/developer/embedded-faq#technical). Ha dedikált kapacitást vásárol, semmi nem korlátozza a beágyazási jogkivonatok létrehozását. Lépjen az [Elérhető szolgáltatások lekérése](https://msdn.microsoft.com/library/mt846473.aspx) részhez az aktuális beágyazott használat százalékos értékének megtekintéséhez.

A kapacitásokat a Power BI felügyeleti portálján kezelheti. Nevezzen ki egy munkaterületi rendszergazdát, aki segít az alkalmazás munkaterületeinek kezelésében. További információkért lásd: [Kapacitások kezelése a Power BI Premium és a Power BI Embedded szolgáltatásban](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

## <a name="next-steps"></a>További lépések

Ha kész létrehozni a Power BI Embedded-kapacitásokat, lásd: [Power BI Embedded-kapacitások létrehozása az Azure Portalon](create-capacity.md).

Ha egyszerű útmutatóra van szüksége, tekintse meg az [Irányítópult, csempe vagy jelentés beágyazása az alkalmazásba](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/) című szakaszt.

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)
