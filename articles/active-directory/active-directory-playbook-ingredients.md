---
title: "Az Azure Active Directory koncepció alkalmazástervezési összetevők |} Microsoft Docs"
description: "Vizsgálatát, és gyorsan végrehajthatja az identitás- és kezelési helyzetek"
services: active-directory
keywords: "az Azure active directory, a forgatókönyv, a koncepció igazolása, PoC"
documentationcenter: 
author: dstefanMSFT
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: ff4a8601b619837d835ec25c26b1f7e69b46ae85
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Az Azure Active Directory igazolása koncepció alkalmazástervezési összetevők 

## <a name="theme"></a>Téma
Az Azure AD lehetővé teszi az identitás- és hozzáférés megoldások a vállalat több területet. A Microsoft besorolni a forgatókönyvek a következő területeken: 

* [Nagy mennyiségű alkalmazásokat, egy identitás](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [A biztonság növelése](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [Méretezéssel önkiszolgáló,](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

A téma a koncepció segítségével a figyelmet a próbálkozások keret mellőzése a vállalati célok meghatározása, gyakran ezek az eseményindítók a koncepció igazolása érdeklő először. 

## <a name="environment"></a>Környezet

Fontos meghatározni a környezetben, ahol a koncepció fog továbbítani részleteit. Ideális esetben hozhat létre rá a koncepció befejeződése után. A célkörnyezet elengedhetetlen, és meg kell találniuk az egyensúlyt azt a valódi lehető és növeli a megkötések vagy további szempontok közé. A tipikus környezeteket POC-khez használható a következők:
* **Éles:** a forgatókönyvek hajtják végre élő környezetében, és már központilag telepítve a Microsoft Cloud services (éles AD, Office 365, az Azure AD bérlő/SSO megoldás). 
* **Felhasználói elfogadás tesztelése ellenőrzését / fejlesztői környezetben:** teszt infrastruktúrával rendelkezik (párhuzamos AD és az esetlegesen Azure AD bérlő/SSO megoldás) a Tesztadatok levő üzemi. Általában a tesztkörnyezet által megosztott több projektet a vállalaton belül.

Az útmutatóban a legtöbb forgatókönyv additívak jellegűek. Ennek eredményeképpen ezek is telepíthető a termelési bérlő anélkül, hogy befolyásolná a koncepció kívüli felhasználók. Ez a dokumentum azt fogja kell létesítő változatok hatása lenne bérlői szintű. Ebben az esetben érdemes figyelembe kell venni egy nem éles környezetben. 


## <a name="target-users"></a>Azon felhasználók megcélzása

Fontos a cél meg, amely a forgatókönyvek intéz, különösen akkor, ha a környezet nem üzemi és tesztkörnyezetben. A koncepció azon felhasználók megcélzása kategóriái a következők:
* **Próbafelhasználók:** valódi felhasználók által használt a megoldás a napi munkakörök használ a fiókhoz a környezetben
* **Tesztfelhasználók:** a környezetben létrehozott fiókot tesztelése 

Ez az útmutató a legtöbb esetben kísérleti felhasználók lehet érvényesíteni. Ez a dokumentum azt fogja kell létesítő cél felhasználói kapcsolatos megfontolások szükség esetén.


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]