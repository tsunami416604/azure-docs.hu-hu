---
title: A fiók beállításait, a LUIS kezelése |} A Microsoft Docs
description: A LUIS webhely segítségével kezelheti a fiók beállításait.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/08/2018
ms.author: diberry
ms.openlocfilehash: 73e90e5ae86db2c2c4625762b285f8c86f0e241b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398045"
---
# <a name="manage-account-and-authoring-key"></a>Fiók és a kulcs létrehozási kezelése
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

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [kulcs létrehozási](luis-concept-keys.md#authoring-key). 

