---
title: A Microsoft-hitelesítési tár (MSAL) alkalmazásokat kíván tesztelni, |} Az Azure
description: Ismerje meg a Microsoft-hitelesítési tár (MSAL) alkalmazásokat kíván tesztelni.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/28/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3495ad5f691ac57b69ab5d3efcd5436cc66e9a6e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307527"
---
# <a name="testing-msal-applications"></a>Az MSAL alkalmazások tesztelése
Ez a cikk ismerteti a Microsoft-hitelesítési tár (MSAL) alkalmazásokat kíván tesztelni, javaslatokat.

## <a name="unit-testing"></a>Egységtesztelés
A MSAL API-k a [builder minta](https://wikipedia.org/wiki/Builder_pattern) erősen. Kapcsolat építői nehéz és fáradságot utánzása. Ehelyett javasoljuk, hogy az összes hitelesítési logikát burkolása mögött egy felületet, és utánzása, amely az alkalmazásban.

## <a name="end-to-end-testing"></a>Teljes körű tesztelés
A teljes körű tesztelés, tesztelési fiókok beállítása, tesztelheti az alkalmazásokat, vagy még a könyvtárak elválasztására. Felhasználónév és jelszó is üzembe helyezhetők a folyamatos integrációs folyamat (például: titkos hozhat létre változókat az Azure DevOps) keresztül. Egy másik olyan stratégia az, hogy ne hitelesítő adatok tesztelése [Key Vault](/azure/key-vault/) és konfigurálása a Key Vault elérése érdekében a tesztek futtató gép), például úgy, hogy a tanúsítvány telepítése. Nyugodtan használhatja az MSAL [stratégiát a Key Vault elérése](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/Microsoft.Identity.Test.LabInfrastructure/KeyVaultSecretsProvider.cs#L112).

Token beszerzéséhez akkor fordul elő, ha a hozzáférési jogkivonatot és a egy frissítési jogkivonat lettek gyorsítótárazva. Az első néhány hónappal az utóbbi egy órás élettartama. Ha a hozzáférési jogkivonat lejár, az MSAL automatikusan használja a frissítési jogkivonatot beszerezni egy új, felhasználói beavatkozás nélkül. Ez a viselkedés a tesztek kiépítése számíthat.

Ha feltételes hozzáférést konfigurált, nehéz lesz körülötte automatizálása. Egy manuális lépés a korábban bejelentkezett felhasználó használó üzletek a feltételes hozzáférés (például többtényezős hitelesítés), amely tokenek hozzáadása az MSAL gyorsítótárába, és ezután támaszkodnak, beavatkozás nélküli token beszerzését, hogy könnyebb lesz.

## <a name="web-apps"></a>Webalkalmazások
Szelén vagy egy azzal egyenértékű technológia segítségével automatizálhatja a webalkalmazás. Beolvassa a felhasználóneveket és a jelszó biztonságos helyről.

## <a name="daemon-apps"></a>Démonalkalmazások
Démon alkalmazások előre telepített titkos kulcsok (jelszavak és tanúsítványok) az Azure Active Directory (AAD) kommunikációhoz használható. A tesztelési környezet üzembe helyezése egy titkos kulcsot, vagy a token-gyorsítótárazási módszer használatával a tesztek kiépítése. A [ügyfél-hitelesítő adatok megadásával](msal-authentication-flows.md#client-credentials) beolvasni a frissítési biztonsági jogkivonat, nem csak hozzáférési jogkivonatokat, amelyek egy óra múlva lejár.

## <a name="native-client-apps"></a>Natív ügyfélalkalmazások
Natív ügyfelek nincsenek tesztelését több megközelítés közül:

* Használja a [felhasználónév/jelszó megadása](msal-authentication-flows.md#usernamepassword) jogkivonatot beolvasni a nem interaktív módon. Ez a folyamat nem javasolt éles környezetben, de ésszerű teszteléséhez használandó.

* Egy keretrendszert használ, mint az Appium vagy Xamarin.Test, hogy automation felületet biztosít az alkalmazás és az MSAL létrehozott böngésző.

* Az MSAL tesz közzé egy indításkiterjesztési pont, amely lehetővé teszi a fejlesztők számára, hogy a saját böngészési élményt beszúrása. A MSAL csapat használja a belső interaktív hitelesítéssel kapcsolatos forgatókönyvek teszteléséhez. Tekintse meg [a .NET projekt tesztelése](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/Microsoft.Identity.Test.Integration.net45/SeleniumTests/InteractiveFlowTests.cs) , hogyan végezheti el egy [szelén által működtetett böngésző](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/tree/master/tests/Microsoft.Identity.Test.Integration.net45/Infrastructure) , amely a hitelesítésnek a kezelésére.

## <a name="xamarin-apps"></a>Xamarin-alkalmazások
Az MSAL csapat jelenleg futtat teszteket egy Xamarin-alkalmazás által használt MSAL.NET; Használjuk [App Center](https://appcenter.ms/apps) eszközeinek kezelésére, tesztfuttatásai stb. A test-keretrendszer [Xamarin.UITest](/appcenter/test-cloud/uitest/). Találtunk egy korlátozást jelent nem lehet tesztelni a rendszer böngészők, csak a beágyazott böngészőket.

Egy teszt keretrendszer kiértékelésekor a mobil terület egy pillantást az Appium, és egyéb tesztelési keretrendszereket, valamint a többi CI/CD-szolgáltatók is rendelkező érdemes.

## <a name="testing-the-token-cache"></a>A jogkivonatok gyorsítótárát tesztelése
Függetlenül attól, milyen platformot használja a MSAL token gyorsítótárban tárolja jogkivonatokat. Egyes platformokon Ön tájékoztatása MSAL szerializálásához ezt a gyorsítótárat. A mobil platformokon MSAL szerializálja a gyorsítótár az Ön számára. Az alkalmazás szempontjából a tokengyorsítótárral felelős három dolgot:

* jogkivonatok tárolására a gyorsítótárban, miután szerezték be őket (például a `AcquireTokenInteractive` metódus)
* jogkivonatok lekérése a gyorsítótárból, hívásakor a `AcquireTokenSilent` metódus
* fiók metaadatok lekérése a gyorsítótárból, hívásakor a `GetAccount` metódus

Ezért ha azt szeretné, gyorsítótáras forgatókönyvek teszteléséhez, érdemes lenne a forgatókönyv írása:

* egy vagy több tokent beszerzése (például [felhasználónév/jelszó megadása](msal-authentication-flows.md#usernamepassword) folyamatot, amely a legegyszerűbb tesztelési)
* Ellenőrizze, hogy `GetAccounts` működik
* Ellenőrizze, hogy `AcquireTokenSilent` működik

Előfordulhat, hogy szeretné is ellenőrizze, hogy:

* az alkalmazás újraindítása nem távolítja el a gyorsítótár
* `AcquireTokenSilent` nem frissíti a frissítési jogkivonat (azt jelenti, hálózati hívást az aad-be), de a hozzáférési jogkivonat szolgál ki, ha még nem járt le. Ezzel és más HTTP kapcsolatos forgatókönyvek érhet el a HTTP-alapú előnyeit.  A .NET-példa: [biztosítása a saját HttpClient](msal-net-provide-httpclient.md)

## <a name="feedback"></a>Visszajelzés
Is [hibák naplózása](developer-support-help-options.md#create-a-github-issue) vagy tesztelési kapcsolatos kérdéseket tehet fel. Jó teszt biztosítása érdekében az egyik a csapat céljait.

## <a name="next-steps"></a>További lépések
További információ a végrehajtási [naplózás](msal-logging.md) az MSAL alkalmazásban.