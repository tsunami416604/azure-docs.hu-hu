---
title: Aláírás-kivonatoló algoritmus módosítása a függő entitás megbízhatóságához az Office 365 |} A Microsoft Docs
description: Ezen a lapon útmutatást nyújt a összevonási megbízhatósági kapcsolatot, és az Office 365 SHA algoritmus módosítása
keywords: SHA1, SHA256, O365, összevonási, aadconnect, AD FS, az ad fs, a módosítás sha, összevonási megbízhatósági kapcsolat, a függő entitás megbízhatóságának
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: billmath
ms.openlocfilehash: 6cdd4a37d6e98b89252dd2fc5fc4ee065c941de2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463541"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Az Office 365, a függő entitás megbízhatóságának aláírás-kivonatoló algoritmus módosítása
## <a name="overview"></a>Áttekintés
Az Active Directory összevonási szolgáltatások (AD FS) a Microsoft Azure Active Directory, győződjön meg arról, hogy azok ne lehessen illetéktelenül jogkivonatai jelentkezik. Az aláírás SHA1, SHA256 vagy alapulhat. Az Azure Active Directory mostantól támogatja az SHA256 algoritmussal aláírt jogkivonatokat, és azt javasoljuk, hogy a jogkivonat-aláíró algoritmus a legmagasabb szintű biztonság SHA256 beállítást. Ez a cikk azt ismerteti, hogy a jogkivonat-aláíró algoritmus szintű biztonságosabb SHA256 beállításához szükséges lépéseket.

>[!NOTE]
>A Microsoft, az algoritmus a jogkivonatok aláírásához biztonságosabb, mint a SHA1, de SHA1 továbbra is egy támogatott beállítás SHA256 használatát javasolja.

## <a name="change-the-token-signing-algorithm"></a>A jogkivonat-aláíró algoritmus módosítása
Miután beállította az aláírási algoritmus az egyik az alábbi két folyamatot, az AD FS jogkivonatok az Office 365, a függő entitás megbízhatóságának, SHA256-jelentkezik. Nem kell további konfigurációs módosításokat, és ez a változás nem befolyásolja a lehetőség az Office 365 vagy más Azure AD-alkalmazások eléréséhez.

### <a name="ad-fs-management-console"></a>AD FS felügyeleti konzol
1. Nyissa meg az AD FS felügyeleti konzol az elsődleges AD FS-kiszolgálón.
2. Bontsa ki az AD FS-csomópontot, és kattintson a **függő entitás Megbízhatóságai**.
3. Kattintson a jobb gombbal az Office 365/Azure-beli függő entitás megbízhatóságához, és válassza ki **tulajdonságok**.
4. Válassza ki a **speciális** lapra, és válassza ki a biztonságos kivonatoló algoritmus SHA256.
5. Kattintson az **OK** gombra.

![SHA256 aláíró algoritmus – MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Az AD FS PowerShell-parancsmagok
1. Bármely AD FS-kiszolgálón nyissa meg a Powershellt a rendszergazdai jogosultságokkal.
2. Állítsa be a biztonságos kivonatoló algoritmus használatával a **Set-AdfsRelyingPartyTrust** parancsmagot.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Emellett olvassa el
* [Az Azure AD Connect a Office 365 bizalmi kapcsolat javítása](how-to-connect-fed-management.md#repairthetrust)

