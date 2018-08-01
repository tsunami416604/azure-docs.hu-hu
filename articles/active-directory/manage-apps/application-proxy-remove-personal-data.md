---
title: Távolítsa el a személyes adatok – Azure Active Directory-alkalmazásproxyval |} A Microsoft Docs
description: Személyes adatok eltávolítása az Azure Active Directory Application Proxy eszközökön telepített összekötők.
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: bccda196be82808e7dc369de3f517490f410e26e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366042"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Személyes adatok eltávolítása az Azure Active Directory Application Proxy  

Az Azure Active Directory Application Proxy kell telepíteni az összekötők az eszközök, ami azt jelenti, hogy van a személyes adatok az eszközökön. A cikk ismerteti a lépéseket az adatvédelmi javítása érdekében a személyes adatok törlése. 


## <a name="where-is-the-personal-data"></a>Hol található a személyes adatok?
Az Application Proxy személyes adatokat írni a következő naplófájl-típusok lehetőség:

- Összekötő-eseménynaplók
- Windows-eseménynaplók,

## <a name="remove-personal-data-from-windows-event-logs"></a>Személyes adatok eltávolítása a Windows-eseménynaplók

A Windows-eseménynaplók az adatmegőrzés konfigurálásáról további információért lásd: [Eseménynaplók beállításai](https://technet.microsoft.com/library/cc952132.aspx). Windows-eseménynaplók kapcsolatos további információkért lásd: [használata a Windows Eseménynapló](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Személyes adatok eltávolítása az összekötő eseménynaplók

Annak érdekében, hogy az alkalmazásproxy-naplók nem rendelkezik a személyes adatok, közül választhat:

- Törölje, vagy megtekintheti az adatokat, amikor szükséges, vagy
- Kapcsolja ki a naplózás

Az alábbi szakaszok segítségével távolítsa el a személyes adatok összekötő eseménynaplók. Az eltávolítási folyamat minden eszközön, amelyre telepítve van az összekötő kell végrehajtania.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Megtekintéséhez, vagy adott adatok exportálása

Megtekintéséhez, vagy adott adatok exportálása, keressen rá az egyes az összekötő eseménynaplók bejegyzéseket. A naplók a következő helyen találhatók `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Mivel a naplók szövegfájlok, használhat [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) kereséséhez kapcsolódó a felhasználó szöveget tartalmazó bejegyzéseket.  

Személyes adatok megkereséséhez keressen UserID naplófájlokat. 

Kerberos által korlátozott delegálást használó alkalmazások által naplózott személyes adatok megkereséséhez keressen felhasználónév típusú ezek az összetevők:

- Helyszíni felhasználó egyszerű neve
- Egyszerű felhasználónév felhasználónév része
- Helyszíni felhasználó egyszerű nevének felhasználónév része
- A helyszíni biztonsági fiókok fiókkezelő (SAM) fiókneve 


### <a name="delete-specific-data"></a>Megadott adatok törlése

Adatok törlése:

1. Indítsa újra a Microsoft Azure AD alkalmazásproxy-összekötő szolgáltatást hozzon létre egy új naplófájl. Az új naplófájl lehetővé teszi, hogy törölni vagy módosítani a régi naplófájlokat. 
2. Kövesse a [adott adatok megtekintése vagy exportálási](#view-or-export-specific-data) folyamat korábban leírt adatokat, akit törölni szükséges. Keresési összekötők naplóját.
3. Törölje a megfelelő naplófájlok helyét, vagy szelektív törlése a személyes adatokat tartalmazó mezőket. Is törölheti a régi naplófájlokat, ha többé már nincs szükség rájuk.

### <a name="turn-off-connector-logs"></a>Kapcsolja ki az összekötő-naplók

Annak érdekében, hogy az összekötő naplók nem tartalmaznak személyes adatokat egy lehetőség, hogy a napló létrehozása kikapcsolása. Leállítja az összekötő naplók létrehozása, távolítsa el a következő kijelölt sor `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Konfiguráció](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>További lépések

Az Application Proxy áttekintéséhez lásd: [biztonságos távoli elérést biztosíthat a helyszíni alkalmazások](application-proxy.md).

