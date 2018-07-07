---
title: A fiók beállításait, a LUIS kezelése |} A Microsoft Docs
description: A LUIS webhely segítségével kezelheti a fiók beállításait.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 1b5caf199cd5bfe0fa462950cc1f7beb3f2872b2
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887606"
---
# <a name="manage-your-luis-account"></a>A LUIS-fiók kezelése
A két kulcsfontosságú adatokat egy LUIS-fiók olyan felhasználói fiók és az Authoring Tool kulcs. A bejelentkezési adatait felügyelt [account.microsoft.com](https://account.microsoft.com). Az Authoring Tool kulcs felügyelje a [LUIS](luis-reference-regions.md) webhely **beállítások** lapot. 

## <a name="authoring-key"></a>Kulcs létrehozási

Ez egyetlen, régióspecifikus szerzői billentyűt, a a **beállítások** lap lehetővé teszi, hogy az összes alkalmazás készítése a [LUIS](luis-reference-regions.md) webhelyet, valamint a [API-k készítése](https://aka.ms/luis-authoring-api). Könnyebb áttekinthetőség érdekében győződjön meg arról, hogy az Authoring Tool kulcs engedélyezett egy [korlátozott](luis-boundaries.md) végpont száma havonta kérdezi le. 

![A LUIS-beállítások lap](./media/luis-how-to-account-settings/account-settings.png)

A szerzői műveletekhez részben kulcsot a saját alkalmazások, valamint bármely közreműködő módon legyen alkalmazások szolgál.

## <a name="authoring-key-regions"></a>Szerzői műveletek legfontosabb területek
Az Authoring Tool kulcs csak a [szerzői műveletek terület](luis-reference-regions.md#publishing-regions). A kulcs nem működik egy másik régióban. 

## <a name="reset-authoring-key"></a>Szerzői kulcs visszaállítása
Ha az Authoring Tool kulcs biztonsága sérül, alaphelyzetbe állítja a kulcsot. A kulcs alaphelyzetbe áll az alkalmazások a [LUIS](luis-reference-regions.md) webhelyén. Ha az Authoring Tool API-kon keresztül az alkalmazások készítésének, értékét módosítani szeretné `Ocp-Apim-Subscription-Key` az új kulccsal. 

## <a name="delete-account"></a>Fiók törlése
Lásd: [adattárolási és -eltávolítási](luis-concept-data-storage.md#accounts) milyen adat törlődik, ha a fiók törlése kapcsolatos információkat. 

## <a name="azure-active-directory-tenant-user"></a>Az Azure Active Directory-bérlői felhasználó
A LUIS a standard szintű Azure Active Directory (Azure AD) jóváhagyási folyamatot használ. 

A bérlői rendszergazda közvetlenül a felhasználó, aki a LUIS használatát az Azure AD-ben biztosított hozzáférést kell működnie. 

A felhasználó először jelentkezik be a LUIS, és megtekinti a rendszergazdai jóváhagyást igénylő felugró párbeszédpanel. A felhasználó felveszi a kapcsolatot a bérlői rendszergazda a folytatás előtt. 

A bérlői rendszergazda második, LUIS bejelentkezik, és egy hozzájárulási folyamat felugró párbeszédpanel látja. Ez az a párbeszédpanelen, a rendszergazda engedélyt kell adnia a felhasználó számára. A rendszergazda fogad az engedélyt, ha a felhasználó nem tudja folytatni az intelligens hangfelismerési szolgáltatással.

Ha a bérlői rendszergazda nem fog bejelentkezni a LUIS, a rendszergazda hozzáférhet [hozzájárulás](https://account.activedirectory.windowsazure.com/r#/applications) a LUIS. 

![Alkalmazás webhelye által az Azure active directory-engedély](./media/luis-how-to-account-settings/tenant-permissions.png)

Ha a bérlői rendszergazda csak meghatározott felhasználóknak, hogy a LUIS használatát szeretné, tekintse meg a [identity blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>A közreműködők több e-mailt a felhasználói fiókok
Ha közreműködők ad hozzá egy LUIS-alkalmazásokon, adja meg a pontos e-mail-cím, LUIS közreműködőként használandó kell közreműködő. Az Azure Active Directory (Azure AD) lehetővé teszi, hogy egyetlen felhasználó felcserélhetők egynél több e-mail-fiókot, amíg a LUIS a felhasználót, hogy jelentkezzen be a közreműködő listában megadott e-mail-cím szükséges. 


## <a name="next-steps"></a>További lépések

Tudjon meg többet a [kulcs létrehozási](luis-concept-keys.md#authoring-key). 

