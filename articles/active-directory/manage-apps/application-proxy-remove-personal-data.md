---
title: Személyes adatok eltávolítása – Azure Active Directory alkalmazásproxy
description: Személyes adatok eltávolítása az Azure Active Directory alkalmazásproxy eszközeire telepített összekötőkből.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275410"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Az Azure Active Directory alkalmazásproxy személyes adatainak eltávolítása

Az Azure Active Directory alkalmazásproxy megköveteli, hogy összekötők telepítése az eszközökre, ami azt jelenti, hogy előfordulhat, hogy a személyes adatok az eszközökön. Ez a cikk az adatvédelem javítása érdekében ismerteti a személyes adatok törlésének lépéseit.

## <a name="where-is-the-personal-data"></a>Hol vannak a személyes adatok?

Az alkalmazásproxy a következő naplótípusokba írhatja a személyes adatokat:

- Összekötő eseménynaplói
- Windows-eseménynaplók

## <a name="remove-personal-data-from-windows-event-logs"></a>Személyes adatok eltávolítása a Windows eseménynaplóiból

A Windows eseménynaplók adatmegőrzési beállításairól az [eseménynaplók beállításai](https://technet.microsoft.com/library/cc952132.aspx)című témakörben olvashat. A Windows eseménynaplóiról a [Windows eseménynapló használata .using](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Személyes adatok eltávolítása az Összekötő eseménynaplóiból

Annak érdekében, hogy az alkalmazásproxy-naplók ne rendelkezzenek személyes adatokkal, a következőket teheti:

- Szükség esetén adatok törlése vagy megtekintése, vagy
- Naplózás kikapcsolása

A következő szakaszok segítségével távolítsa el a személyes adatokat az összekötő eseménynaplókból. Be kell fejeznie az eltávolítási folyamatot minden olyan eszközön, amelyre az összekötő telepítve van.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Adott adatok megtekintése vagy exportálása

Adott adatok megtekintéséhez vagy exportálásához keresse meg a kapcsolódó bejegyzéseket az összekötő minden eseménynaplójában. A naplók a `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`helyen találhatók.

Mivel a naplók szöveges fájlok, [a findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) segítségével kereshet a felhasználóhoz kapcsolódó szöveges bejegyzéseket.  

Személyes adatok megkereséséhez keressen a naplófájlokban a UserID elemre.

Kerberos korlátozott delegálást használó alkalmazás által naplózott személyes adatok megkereséséhez keresse meg a felhasználónév típusú összetevőket:

- Helyszíni egyszerű felhasználónév
- Az egyszerű felhasználónév része
- A helyszíni egyszerű felhasználónév felhasználóneve
- Helyszíni biztonsági fiókkezelő (SAM) fiókneve

### <a name="delete-specific-data"></a>Adott adatok törlése

Adott adatok törlése:

1. Új naplófájl létrehozásához indítsa újra a Microsoft Azure AD alkalmazásproxy-összekötő szolgáltatást. Az új naplófájl lehetővé teszi a régi naplófájlok törlését vagy módosítását. 
1. A korábban leírt [konkrét adatfolyamatok megtekintése vagy exportálása](#view-or-export-specific-data) során keresse meg a törölendő adatokat. Keressen az összes összekötő naplóban.
1. Vagy törölje a megfelelő naplófájlokat, vagy szelektíven törölje a személyes adatokat tartalmazó mezőket. Törölheti az összes régi naplófájlt is, ha már nincs rájuk szüksége.

### <a name="turn-off-connector-logs"></a>Összekötőnaplók kikapcsolása

Az egyik lehetőség annak biztosítására, hogy az összekötő naplók nem tartalmaznak személyes adatokat, hogy kapcsolja ki a napló létrehozását. Az összekötőnaplók létrehozásának leállításához távolítsa el `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`a következő kiemelt sort a alkalmazásból.

![Az eltávolítandó kiemelt kódrészlettel rendelkező kódrészlet](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>További lépések

Az alkalmazásproxy áttekintését a [Helyszíni alkalmazások biztonságos távelérésének biztosítása](application-proxy.md)című témakörben találja.