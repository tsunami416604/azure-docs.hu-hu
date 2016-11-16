---
title: "Kereskedelmipartner-egyezmény létrehozása az Azure App Service-ben | Microsoft Docs"
description: "Kereskedelmipartner-egyezmények létrehozása"
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajram
manager: erikre
editor: 
ms.assetid: 319e46fa-fd81-4730-a742-768bf1676972
ms.service: logic-apps
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/23/2016
ms.author: rajram
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e41ac0e91bd66fbc7df08b4397e78377021fcbca


---
# <a name="creating-a-trading-partner-agreement"></a>Kereskedelmipartner-egyezmény létrehozása
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Kereskedelmi partnereknek a vállalatközi kommunikációban részt vevő entitásokat nevezzük. A két partner között kialakított kapcsolatokat *egyezményeknek* nevezzük. A létrejövő protokoll- vagy átvitelspecifikus egyezményt annak alapján alakítják ki, hogy a két partner milyen típusú kommunikációt szeretne végezni. Az Azure App Service többek között az alábbi vállalatközi protokollokat és átviteli módokat támogatja:

* AS2 (Applicability Statement 2)
* EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport, UN/EDIFACT)
* X12 (ASC X12)

### <a name="biztalk-api-apps-that-support-b2b-scenarios"></a>A vállalatközi megoldásokat támogató BizTalk API-alkalmazások
Az alábbi API-alkalmazások használhatók az Azure Portal gazdag és magától értetődő felhasználói felületével:

## <a name="biztalk-trading-partner-management-tpm"></a>BizTalk Trading Partner Management (TPM)
* Partnerek, profilok és identitások létrehozására és kezelésére szolgál
* EDI-sémák tárolása és kezelése
* Az AS2-protokollban használt tanúsítványok tárolása és kezelése
* AS2-egyezmények létrehozása és kezelése
* EDIFACT-egyezmények létrehozása és kezelése (a kötegelés támogatásával a küldési oldalon)
* X12-egyezmények létrehozása és kezelése (a kötegelés támogatásával a küldési oldalon)

![][1]

## <a name="as2-connector"></a>AS2-összekötő
* A kapcsolódó TPM API-alkalmazáspéldány által meghatározott AS2-egyezmények végrehajtása
* Hibaelhárításra használható AS2-feldolgozási/-nyomkövetési információk megjelenítése

## <a name="biztalk-edifact"></a>BizTalk EDIFACT
* A kapcsolódó TPM API-alkalmazáspéldány által meghatározott EDIFACT-egyezmények végrehajtása
* Hibaelhárításra használható EDIFACT-feldolgozási/-nyomkövetési információk megjelenítése
* A kapcsolódó TPM API-alkalmazáspéldányban szereplő EDIFACT-egyezmény(ek) alapján lehetővé teszi a kötegek állapotkezelését (indítás és leállítás) 

## <a name="biztalk-x12"></a>BizTalk X12
* A kapcsolódó TPM API-alkalmazáspéldány által meghatározott X12-egyezmények végrehajtása 
* Hibaelhárításra használható X12-feldolgozási/-nyomkövetési információk megjelenítése
* A kapcsolódó TPM API-alkalmazáspéldányban szereplő X12-egyezmény(ek) alapján lehetővé teszi a kötegek állapotkezelését (indítás és leállítás)

Ahogy azt már korábban is említettük, az AS2, X12 és EDIFACT API-alkalmazások megfelelő működéséhez a TPM API-alkalmazás szükséges.

## <a name="getting-started"></a>Első lépések
Kereskedelmipartner-egyezmények létrehozása:

1. Hozzon létre egy példányt a **BizTalk Trading Partner Management** összekötőből. Ennek működéséhez üres SQL Database-adatbázis szükséges. Mielőtt hozzálát, gondoskodjon róla, hogy az üres adatbázis elérhető legyen, és használatra készen álljon.
2. Töltse fel az egyezményekhez szükséges sémákat és tanúsítványokat. Ehhez keresse meg a létrehozott TPM-példányt, és lépjen be a „Sémák” és/vagy „Tanúsítványok” részbe.
3. Keresse meg a létrehozott TPM-példányt, és lépjen be a **Partnerek** részbe.
4. Hozza létre a kívánt partnereket. Módosítsa a profilokat a kívánt módon, és adja hozzá a kívánt identitásokat.
5. Ezt követően az **Egyezmények** résznél hozza létre az egyezményeket. Az egyezmények létrehozásánál meg kell adnia a használni kívánt protokollt is. A további beállítási lehetőségek a kiválasztott protokolltól függően jelennek meg.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png




<!--HONumber=Nov16_HO2-->


