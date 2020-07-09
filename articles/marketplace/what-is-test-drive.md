---
title: Mit jelent a tesztverzió? Microsoft kereskedelmi piactér
description: A Marketplace Test Drive funkció magyarázata
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: 95ca527d8f7912e2c455f94036fe61828b220257
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121469"
---
# <a name="what-is-a-test-drive"></a>Mit jelent a tesztverzió?

A test Drive nagyszerű lehetőséget nyújt arra, hogy ajánlatot nyújtson a potenciális ügyfelek számára azáltal, hogy a *vásárlás előtt próbálkozik a kipróbálással*, ami nagyobb átalakítást és magas szintű minősítést eredményezett. A tesztelési meghajtó valós megvalósítási forgatókönyvben biztosítja a termék életbe lépését, miközben magas szintű minősítésű érdeklődőket is generál.

A tesztelési meghajtók olyan felügyelt példányok, amelyek igény szerint központilag telepítik a megoldást vagy az alkalmazást. Miután hozzárendelte a test Drive-példányt, rendelkezésre áll egy meghatározott időtartamra, majd törölve, hogy helyet hozzon létre egy másik ügyfél számára.

Közzétevőként kezelheti és konfigurálhatja a tesztelési meghajtó beállításait a partner Centerben. A technikai konfiguráció részletei a használt ajánlat típusától függően változnak. Részletes útmutatásért tekintse meg a témakör végén található, a [következő lépésre](#next-step) mutató hivatkozást.

A potenciális ügyfelek a kereskedelmi piactéren is felfedezhetik a tesztet. Megadják a kapcsolattartási adatokat, és elfogadják az ajánlat használati feltételeit és adatvédelmi szabályzatát, majd hozzáférhetnek az előre konfigurált környezethez, hogy meghatározott időtartamra kipróbálják. Az ügyfelek a termék főbb funkcióit és előnyeit igénybe vevő, saját irányítású próbaidőszakot kapnak, és értékes érdeklődőket kapnak.

## <a name="how-does-it-work"></a>Hogyan működik?

Közzétevőként kezelheti és konfigurálhatja a felügyeleti meghajtó beállításait a partner centeren belül. A telepítés után egy felügyelt példány lesz, amelyet igény szerint üzembe kell helyezni az ügyfél számára. Miután hozzárendelte a test Drive-példányt, rendelkezésre áll egy meghatározott időtartamra, majd törölve, hogy helyet hozzon létre egy másik ügyfél számára.

## <a name="types-of-test-drives"></a>A tesztelési meghajtók típusai

A kereskedelmi piactéren különböző tesztelési meghajtók érhetők el, a termékek, a forgatókönyvek és a piactér típusától függően:

- Azure Resource Manager
- Szolgáltatott tesztelési meghajtó
    - Dynamics 365 for Business Central
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Logikai alkalmazás
- Power BI

A tesztelési meghajtók egyikének konfigurálásával kapcsolatos részletekért tekintse meg a témakör végén található [következő lépés](#next-step) című szakaszát.

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó

Ez a központi telepítési sablon tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják. Ez az egyetlen tesztvezetés lehetőség a virtuális gépekhez vagy az Azure-alkalmazások ajánlatához.

### <a name="hosted-test-drive"></a>Szolgáltatott tesztelési meghajtó

Az üzemeltetett tesztvezetés eltávolítja a telepítő összetettségét azáltal, hogy a Microsoft-gazdagépet és karbantartja a szolgáltatást, amely elvégzi a tesztelési meghajtó felhasználó általi kiépítését, üzembe helyezését és megszüntetését. Ha a Microsoft AppSource ajánlata van, készítse el a tesztvezetés egy Dynamics AX/CRM-példánnyal való kapcsolódáshoz, vagy az Azure-on kívül más erőforrásokhoz. Használja ezt a típust a AppSource-ajánlatokhoz a Dynamics 365-ajánlatokkal való kapcsolódáshoz:

- A [Dynamics 365 for Business Central](partner-center-portal/create-new-operations-offer.md) egy üzleti központi vállalati erőforrás-tervezési rendszer, például a pénzügy, a műveletek, az ellátási lánc és a CRM esetében használható.
- Használja a Dynamics 365-et a [Customer engagement](partner-center-portal/create-new-customer-engagement-offer.md) szolgáltatáshoz, például az értékesítés, a szolgáltatás, a Project Service és a Field Service szolgáltatáshoz.
- A [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) for Finance and Operations vállalati erőforrás-tervezési rendszer, például a pénzügy, a műveletek és a gyártás, az ellátási lánc használata.

### <a name="logic-app-test-drive"></a>Logic app Test Drive

Ezt a típusú tesztelési meghajtót nem a Microsoft üzemelteti. Használatával kapcsolódhat Dynamics 365-ajánlathoz vagy más egyéni erőforráshoz.

### <a name="power-bi-test-drive"></a>Power BI tesztelési meghajtó

Ez egyszerűen egy egyéni kialakítású irányítópultra mutató beágyazott hivatkozás. Minden olyan termék, amely csak interaktív Power BI vizualizációt mutat be, ezt a típusú tesztelési meghajtót kell használnia.

## <a name="transforming-examples"></a>Példák átalakítása

Az erőforrások egy tesztelési meghajtóba való bekapcsolásának folyamata ijesztő lehet. Tekintse át ezeket a példákat a jelenlegi architektúrák legjobb átalakításához.

[Webhely-sablon átalakítása tesztelési meghajtóra](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Virtuálisgép-sablon átalakítása tesztelési meghajtóra](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Meglévő Resource Manager-sablon átalakítása tesztelési meghajtóra](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Érdeklődők előállítása a test Drive-ból

A kereskedelmi Piactéri tesztvezetés nagyszerű eszköz a marketingesek számára. Azt javasoljuk, hogy a piacra jutást a piaci megjelenéssel kapcsolatos erőfeszítésekben érdemes beépíteni. Részletes útmutatásért tekintse [meg a kereskedelmi Marketplace-ajánlat ügyfeleinek vezetőit](https://github.com/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Ha lezárta a tesztelési meghajtóval rendelkező üzletet, ügyeljen arra, hogy regisztrálja azt a [Microsoft partner Sales kapcsolódási](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect)szolgáltatásban. Azt is szeretnénk hallani az ügyfél-WINS-ről, hogy a tesztvezetés milyen szerepet játszott.

## <a name="other-resources"></a>Egyéb erőforrások

További tesztelési meghajtó erőforrásai:

- [Gyakorlati tanácsok](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása)

## <a name="next-step"></a>Következő lépés

- [Tesztverzió műszaki konfigurálása](test-drive-technical-configuration.md)
