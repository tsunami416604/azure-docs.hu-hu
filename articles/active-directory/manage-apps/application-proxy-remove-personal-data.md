---
title: Személyes adattárolás eltávolítása – Azure Active Directory Application Proxy
description: A személyes adatok eltávolítása a Azure Active Directory Application Proxy eszközökön telepített összekötők közül.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe97956d99bd5c677e499b532ef85a1bb4d324ef
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275410"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Azure Active Directory Application Proxy személyes adatfájljainak eltávolítása

A Azure Active Directory Application Proxy megköveteli, hogy az eszközökön telepítsen összekötőket, ami azt jelenti, hogy az eszközökön személyes adatai lehetnek. Ez a cikk azokat a lépéseket ismerteti, amelyekkel a személyes adatok az adatvédelem javítása érdekében törölhetők.

## <a name="where-is-the-personal-data"></a>Hol található a személyes adathalmaz?

Az Application proxy a következő típusú naplókba írhatja a személyes adattípusokat:

- Összekötő-eseménynaplók
- Windows-eseménynaplók

## <a name="remove-personal-data-from-windows-event-logs"></a>Személyes adatok eltávolítása a Windows-eseménynaplóból

További információ a Windows-eseménynaplók adatmegőrzésének konfigurálásáról: [Eseménynaplók beállításai](https://technet.microsoft.com/library/cc952132.aspx). A Windows-eseménynaplók megismeréséhez lásd: [a Windows Eseménynapló használata](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Személyes adatok eltávolítása az összekötő eseménynaplóból

Annak biztosítása érdekében, hogy az alkalmazásproxy naplói ne rendelkezzenek személyes adattal, a következők közül választhat:

- Szükség esetén törölheti vagy megtekintheti az adatmegjelenítést, vagy
- Naplózás kikapcsolása

A következő részben az összekötő-eseménynaplók személyes adatait távolíthatja el. Az eltávolítási folyamatot minden olyan eszközön el kell végeznie, amelyen az összekötő telepítve van.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Megadott adatértékek megtekintése vagy exportálása

Adott adatok megtekintéséhez vagy exportálásához keresse meg a kapcsolódó bejegyzéseket az egyes összekötő-eseménynaplókban. A naplók a következő helyen találhatók: `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`.

Mivel a naplók szövegfájlok, a [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) használatával keresheti meg a felhasználóhoz kapcsolódó szöveges bejegyzéseket.  

Személyes adatkereséshez keresse meg a felhasználói azonosítóhoz tartozó naplófájlokat.

A Kerberos által korlátozott delegálást használó alkalmazás által naplózott személyes adatkeresések kereséséhez keresse meg a Felhasználónév típusa következő összetevőit:

- Helyszíni egyszerű Felhasználónév
- Felhasználónév része az egyszerű felhasználónévnek
- A helyszíni egyszerű felhasználónév része
- Helyszíni biztonsági fiókkezelő (SAM) fiók neve

### <a name="delete-specific-data"></a>Megadott adatértékek törlése

Megadott adatértékek törlése:

1. Indítsa újra a Microsoft Azure AD alkalmazásproxy-összekötő szolgáltatást egy új naplófájl létrehozásához. Az új naplófájl lehetővé teszi a régi naplófájlok törlését vagy módosítását. 
1. A korábban ismertetett adatfolyamatok [megtekintéséhez vagy exportálásához](#view-or-export-specific-data) kövesse a törölni kívánt adatokat. Keresse meg az összes összekötő naplóját.
1. Törölje a kapcsolódó naplófájlokat, vagy törölje a személyes adatokat tartalmazó mezőket. Ha már nincs szüksége rájuk, törölheti az összes régi naplófájlt is.

### <a name="turn-off-connector-logs"></a>Összekötő naplófájljainak kikapcsolása

Az egyik lehetőség annak biztosítására, hogy az összekötő naplófájljai ne tartalmazzanak személyes adattartalmakat a napló létrehozásának kikapcsolásához. Az összekötő-naplók létrehozásának leállításához távolítsa el a következő Kiemelt sort a `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`ból.

![Megjelenít egy kódrészletet az eltávolítandó Kiemelt kóddal](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Következő lépések

Az alkalmazásproxy áttekintését lásd: [biztonságos távoli hozzáférés biztosítása a helyszíni alkalmazásokhoz](application-proxy.md).