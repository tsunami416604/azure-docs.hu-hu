---
title: Egyéni szabályzatok Azure Active Directory B2C | Microsoft Docs
description: Ismerkedjen meg Azure Active Directory B2C egyéni szabályzatokkal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f72aedb010301f9c7b12778432c4f10feb10f7a3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189232"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Egyéni házirendek a Azure Active Directory B2Cban

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az egyéni házirendek olyan konfigurációs fájlok, amelyek meghatározzák a Azure Active Directory B2C (Azure AD B2C) bérlő viselkedését. A felhasználói folyamatok előre definiálva vannak a Azure AD B2C-portálon a leggyakoribb identitási feladatokhoz. Az egyéni házirendek teljes mértékben szerkeszthetők egy identitás-fejlesztőtől számos különböző feladat elvégzéséhez.

## <a name="comparing-user-flows-and-custom-policies"></a>Felhasználói folyamatok és egyéni házirendek összehasonlítása

| | Felhasználói folyamatok | Egyéni szabályzatok |
|-|-------------------|-----------------|
| Megcélzott felhasználók | Az összes alkalmazás-fejlesztő személyazonossági szakértelemmel vagy anélkül. | Identitás-szakemberek, rendszerintegrátorok, tanácsadók és belső identitású csapatok. Ezek kényelmesek az OpenID Connect-folyamatokkal, valamint az identitás-szolgáltatók és a jogcímek hitelesítésének megismerésére. |
| Konfigurációs módszer | Azure Portal egy felhasználóbarát felhasználói felülettel (UI). | Közvetlenül szerkesztheti az XML-fájlokat, majd feltöltheti őket a Azure Portalba. |
| Felhasználói felület testreszabása | A felhasználói felület teljes testreszabása HTML-, CSS-és JavaScript-beállításokkal.<br><br>Többnyelvű támogatás egyéni karakterláncokkal. | azonos |
| Attribútumok testreszabása | Standard és egyéni attribútumok. | azonos |
| Jogkivonat-és munkamenet-kezelés | Egyéni jogkivonat és több munkamenet-beállítás. | azonos |
| Identitásszolgáltatók | Előre definiált helyi vagy közösségi szolgáltató és a legtöbb OIDC-identitás szolgáltatója, mint például az Azure Active Directory bérlők közötti összevonás. | Szabványokon alapuló OIDC, OAUTH és SAML.  A REST API-kkal való integráció használatával a hitelesítés is lehetséges. |
| Identitással kapcsolatos feladatok | Regisztráció vagy bejelentkezés helyi vagy számos közösségi fiókkal.<br><br>Önkiszolgáló jelszó-visszaállítás.<br><br>Profil szerkesztése.<br><br>Multi-Factor Authentication.<br><br>Jogkivonatok és munkamenetek testreszabása.<br><br>Hozzáférési jogkivonat folyamatai. | Hajtsa végre ugyanazokat a feladatokat, mint a felhasználói folyamatok egyéni identitás-szolgáltatók használatával vagy egyéni hatókörök használata.<br><br>Hozzon létre egy felhasználói fiókot egy másik rendszeren a regisztráció időpontjában.<br><br>Üdvözlő e-mail küldése a saját e-mail szolgáltatójának használatával.<br><br>Használjon Azure AD B2Con kívüli felhasználói tárolót.<br><br>A felhasználó által megadott adatok érvényesítése egy megbízható rendszerrel egy API használatával. |

## <a name="policy-files"></a>Házirend-fájlok

A rendszer a következő három típusú házirend-fájlt használja:

- **Alapfájl** – a definíciók többségét tartalmazza. Javasoljuk, hogy a hibák elhárításához és a szabályzatok hosszú távú karbantartásához legalább a fájl módosításait végezze el.
- **Kiterjesztések fájl** – a bérlő egyedi konfigurációs módosításait tartalmazza.
- **Függő entitás (RP) fájlja** – az alkalmazás vagy szolgáltatás által közvetlenül meghívott, egyetlen feladat által irányított fájl (más néven függő entitás). Mindegyik egyedi feladat saját RP-t igényel, és a márkaépítési követelményektől függően a szám a használati esetek teljes száma x számú.

A felhasználó Azure AD B2C a fentiekben látható három fájlra, de a fejlesztő csak az RP-fájlt látja, míg a Azure Portal módosítja a háttérben a kiterjesztéseket tartalmazó fájlt.

## <a name="custom-policy-core-concepts"></a>Egyéni szabályzat – alapfogalmak

Az Azure-beli ügyfél-identitás-és hozzáférés-kezelési (CIAM) szolgáltatás az alábbiakat tartalmazza:

- Olyan felhasználói könyvtár, amely Microsoft Graph használatával érhető el, és amely a helyi fiókok és az összevont fiókok felhasználói adataival rendelkezik.
- Hozzáférés az **Identity Experience keretrendszerhez** , amely összehangolja a felhasználók és az entitások közötti bizalmi kapcsolatot, és a közöttük lévő jogcímeket a személyazonossági vagy hozzáférés-kezelési feladatok elvégzéséhez
- Egy biztonságijogkivonat-szolgáltatás (STS), amely azonosító jogkivonatokat, frissítési jogkivonatokat és hozzáférési jogkivonatokat (és egyenértékű SAML-kijelentéseket) állít ki, és ellenőrzi, hogy az erőforrások védelme megtörtént-e.

A Azure AD B2C identitás-szolgáltatók, felhasználók, más rendszerek és a helyi felhasználói könyvtár használatával kommunikálnak az identitási feladatok eléréséhez. Például jelentkezzen be egy felhasználóval, regisztráljon egy új felhasználót, vagy állítsa alaphelyzetbe a jelszót. Az identitási élmény keretrendszere és a szabályzat (más néven felhasználói út vagy megbízhatósági keretrendszer-házirend) többrésztvevős megbízhatóságot hoz létre, és explicit módon meghatározza a szereplőkkel, a műveletekkel, a protokollokkal és a végrehajtandó lépések sorozatával kapcsolatos feladatokat.

Az Identity Experience Framework egy teljes körűen konfigurálható, házirend által vezérelt, felhőalapú Azure-platform, amely az entitások közötti megbízhatósági kapcsolatot hangolja össze a szabványos protokoll-formátumokban, mint például az OpenID Connect, a OAuth, az SAML és néhány nem standard, például REST API-alapú rendszer-rendszerbeli jogcímek cseréje. A keretrendszer felhasználóbarát, fehér címkével ellátott, a HTML-t és a CSS-t támogató tapasztalatokat hoz létre.

Az egyéni szabályzatok egy vagy több XML formátumú fájlként jelennek meg, egymásra hierarchikus sorrendben hivatkozva. Az XML-elemek meghatározzák a jogcímek sémáját, a jogcímek átalakítását, a tartalmi definíciókat, a jogcím-szolgáltatókat, a technikai profilokat és a felhasználói útvonalak előkészítésének lépéseit a többi elem Az egyéni szabályzatok egy vagy több olyan XML-fájlhoz érhetők el, amelyet az Identity Experience Framework hajt végre, amikor egy függő entitás meghívja őket. Az egyéni házirendeket konfiguráló fejlesztőknek alapos részletességgel kell megadniuk a megbízható kapcsolatokat, hogy tartalmazzák a metaadatok végpontját, a pontos jogcím-definíciókat, valamint a titkok, kulcsok és tanúsítványok konfigurálását az egyes identitás-szolgáltatók igényei szerint.

### <a name="inheritance-model"></a>Öröklési modell

Amikor egy alkalmazás meghívja az RP-házirend fájlját, a Azure AD B2C Identity Experience Framework az alapfájlból származó összes elemet hozzáadja a kiterjesztések fájlból, majd az RP-házirend fájlból az aktuális házirend összeállításához.  Az RP-fájlban szereplő azonos típusú és nevű elemek felülbírálják a bővítmények és a bővítmények felülbírálásának alapjait.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)
