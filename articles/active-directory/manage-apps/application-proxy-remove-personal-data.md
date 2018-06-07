---
title: Távolítsa el a személyes adatok - Azure Active Directory Alkalmazásproxyjával |} Microsoft Docs
description: Távolítsa el a személyes adatok az összekötők, az Azure Active Directory Alkalmazásproxyjával telepítve azokon az eszközökön.
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 94cf0464aca3c46e0c6425b0fb3e24fcd767f95c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655053"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Az Azure Active Directory Alkalmazásproxyjával személyes adatok eltávolítása  

Az Azure Active Directory Alkalmazásproxyjával kell telepíteni összekötők az eszközök, ami azt jelenti, hogy lehet személyes adatokat az eszközök. Ez a cikk ismerteti, hogy az javítása az adatvédelmi személyes adatok törlése lépéseit. 


## <a name="where-is-the-personal-data"></a>Hol található a személyes adatok?
Azonban lehetséges a személyes adatokat írni a következő napló típusok alkalmazásproxy:

- Összekötő eseménynaplók
- Windows-eseménynaplók,

## <a name="remove-personal-data-from-windows-event-logs"></a>Személyes adatok eltávolítása Windows-Eseménynapló

A Windows eseménynaplóiban keresse meg az adatok megőrzése konfigurálásával kapcsolatos további információkért lásd: [Eseménynaplók beállításai](https://technet.microsoft.com/library/cc952132.aspx). Című témakörben olvashat a Windows eseménynaplóiban keresse meg, [használata a Windows Eseménynapló](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Távolítsa el a személyes adatok összekötő Eseménynapló

Biztosítja az alkalmazásproxy naplók nem személyes adatokat, a következő lehetőségek közül választhat:

- Törölje vagy megtekintheti az adatokat, ha szükséges, vagy
- Kapcsolja ki a naplózás

A következő szakaszok segítségével távolítsa el a személyes adatok az összekötő eseménynaplóit. Az összes olyan eszköz, amelyre az összekötő telepítve van az eltávolítási eljárás elvégzése után.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Megtekintése vagy adott adatok exportálása

Megtekintése vagy adott adatok exportálása, keresse meg az egyes az összekötő eseménynaplók bejegyzéseket. A naplók `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Mivel a naplók szövegfájlok, használhat [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) keresendő szöveget tartalmazó bejegyzéseket a felhasználó kapcsolódik.  

UserID naplófájlok keressen a személyes adatok kereséséhez. 

Kerberos által korlátozott delegálást használó alkalmazások által naplózott személyes adatok kereséséhez, keresse meg a felhasználónév típusú összetevők:

- Helyszíni felhasználó egyszerű neve
- Egyszerű felhasználónév felhasználónév része
- Helyszíni felhasználó egyszerű nevének felhasználónév része
- A helyi biztonsági fiókok fiókkezelő (SAM) fiók neve 


### <a name="delete-specific-data"></a>Adott adatok törlése

Adatok törlése:

1. Indítsa újra a Microsoft Azure AD alkalmazásproxy-összekötő szolgáltatást hozzon létre új naplófájlt. Az új naplófájl lehetővé teszi, hogy törölni vagy módosítani a régi naplófájlokat. 
2. Kövesse a [nézet vagy exportálási adatokat](#view-or-export-specific-data) kapcsolatos információk, amelyek törölni kell a fentiekben ismertetett folyamatot. Az összekötő a naplók keresése.
3. Törölje a megfelelő naplófájlok helyét, vagy a személyes adatokat tartalmazó mezők szelektív törlése. Is törölheti a régi naplófájlokat, ha akkor nincs szükség rájuk.

### <a name="turn-off-connector-logs"></a>Kapcsolja ki a connector naplófájljai

Kapcsolja ki a napló létrehozása egy beállítással biztosíthatja, hogy a connector naplófájljaiban nem tartalmaz személyes adatokat is. Leállítja az összekötő naplók generálásához, távolítsa el a következő kijelölt sor `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Konfiguráció](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>További lépések

Alkalmazásproxy áttekintését lásd: [hogyan biztosíthat biztonságos távoli hozzáférést a helyszíni alkalmazások](application-proxy.md).

