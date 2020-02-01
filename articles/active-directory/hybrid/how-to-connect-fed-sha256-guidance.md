---
title: Az Office 365 függő entitás megbízhatóságának módosítására szolgáló aláírás-kivonatoló algoritmus módosítása – Azure
description: Ez az oldal útmutatást nyújt az SHA-algoritmus az Office 365-vel való összevonási megbízhatóságának módosításához
keywords: SHA1, SHA256, O365, összevonás, aadconnect, ADFS, AD FS, Change SHA, összevonási megbízhatóság, függő entitás megbízhatósága
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2233b434fda628dcf812a62f06541fc4b0296aba
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897352"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Az Office 365 függő entitás megbízhatóságának módosítása az aláírás-kivonatoló algoritmusban
## <a name="overview"></a>Áttekintés
Active Directory összevonási szolgáltatások (AD FS) (AD FS) aláírja a tokeneit Microsoft Azure Active Directory, így biztosítva, hogy nem módosíthatók a használatával. Ez az aláírás SHA1-vagy SHA256-alapú lehet. A Azure Active Directory mostantól támogatja a SHA256 algoritmussal aláírt tokeneket, és azt javasoljuk, hogy a token-aláíró algoritmust a legmagasabb szintű biztonság SHA256 állítsa be. Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek ahhoz, hogy a jogkivonat-aláíró algoritmust a biztonságosabb SHA256 szintre állítsa.

>[!NOTE]
>A Microsoft azt javasolja, hogy a SHA256 az aláírási tokenekhez használt algoritmusként használják, mivel az sokkal biztonságosabb, mint az SHA1, de az SHA1 továbbra is támogatott lehetőség marad.

## <a name="change-the-token-signing-algorithm"></a>Jogkivonat-aláíró algoritmus módosítása
Miután beállította az aláírási algoritmust az alábbi két folyamat egyikével, AD FS aláírja az Office 365 függő entitás megbízhatóságát a SHA256-mel. Nem kell további konfigurációs módosításokat végeznie, és ez a változás nem befolyásolja az Office 365-vagy más Azure AD-alkalmazások elérésének lehetőségét.

### <a name="ad-fs-management-console"></a>AD FS felügyeleti konzol
1. Nyissa meg az AD FS felügyeleti konzolt az elsődleges AD FS kiszolgálón.
2. Bontsa ki a AD FS csomópontot, és kattintson a **függő entitások megbízhatóságai**elemre.
3. Kattintson a jobb gombbal az Office 365/Azure függő entitás megbízhatóságára, és válassza a **Tulajdonságok**lehetőséget.
4. Válassza a **speciális** lapot, és válassza ki a biztonságos KIVONATOLÓ algoritmus sha256.
5. Kattintson az **OK** gombra.

![SHA256-aláírási algoritmus – MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>PowerShell-parancsmagok AD FS
1. Bármelyik AD FS-kiszolgálón nyissa meg a PowerShellt rendszergazdai jogosultságok alatt.
2. Állítsa be a biztonságos kivonatoló algoritmust a **set-AdfsRelyingPartyTrust** parancsmag használatával.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>További információk
* [Az Office 365-alapú megbízhatóságának javítása Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

