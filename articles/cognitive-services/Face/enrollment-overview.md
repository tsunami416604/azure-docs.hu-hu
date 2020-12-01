---
title: Face API regisztráció áttekintése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan regisztrálhatók a felhasználók a Face Recognition szolgáltatásban.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: ac5106aa661cb2baea31ee15d57e9c6fac8c7192
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350330"
---
# <a name="face-api-enrollment"></a>Face API regisztráció

A Cognitive Services Face API a Face ellenőrzéshez vagy az azonosításhoz egy **LargePersonGroup** kell regisztrálnia. Ez a részletes útmutató bemutatja az ajánlott eljárásokat a felhasználóktól származó értelmes bejelentések összegyűjtéséhez, valamint példaként, hogy magas színvonalú regisztrációkat hozzon létre, amelyek optimalizálják az elismerés pontosságát.  

## <a name="meaningful-consent"></a>Értelmes beleegyezett 

Az Arcfelismerés beléptetési alkalmazásának egyik fő célja, hogy lehetővé teszi a felhasználók számára, hogy bizonyos célokra, például a munkaterületen való hozzáféréshez az arcaik képeit használják fel. Mivel az arc-felismerési technológiák bizalmas személyes adatok gyűjtésére is felfoghatók, különösen fontos, hogy a beleegyező adatokat az átlátható és tiszteletteljes módon kell megtenni. A felhasználók akkor is hasznosak lehetnek, ha felhatalmazza őket arra, hogy a számukra legmegfelelőbb döntést hozzanak.   

A Microsoft felhasználói kutatása, a Microsoft felelős AI alapelvei és a [külső kutatás](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf)alapján azt találtuk, hogy a beleegyezés hasznos, ha a következőket kínálja a technológiában regisztrált felhasználóknak:

* Figyelem: a felhasználóknak nem kell kétségbe lépniük, amikor a rendszer kéri, hogy adja meg a saját arcát sablonját vagy a beléptetési fényképeket. 
* Megértés: a felhasználóknak pontosan le kell tudniuk írniuk a saját szavaikat, hogy mire kérték őket, kinek, milyen célból és milyen garanciákkal. 
* Választás szabadsága: a felhasználók nem tudnak kényszerített vagy manipulált, ha kiválasztják, hogy szeretne-e beleegyezni és bejelentkezni az arc-felismerésbe. 
* Vezérlés: a felhasználóknak képesnek kell lenniük visszavonni a hozzájárulásukat, és bármikor törölniük az adatvesztést. 

Ez a szakasz útmutatást nyújt a beléptetési alkalmazások arc-felismeréshez való fejlesztéséhez. Ez az útmutató a Microsoft felhasználói kutatása alapján lett kifejlesztve, amely az egyéni felhasználók beléptetésére szolgál az arc-felismerés során a létrehozáshoz. Ezért előfordulhat, hogy ezek a javaslatok nem vonatkoznak az összes arc-felismerési megoldásra. A Face API felelős használata erősen függ attól a konkrét környezettől, amelyben integrálva van, ezért a javaslatok rangsorolását és alkalmazását a forgatókönyvhöz kell igazítani. 

> [!NOTE]
> Az Ön felelőssége, hogy a beléptetési alkalmazást a megfelelő jogi követelményekkel igazítsa az Ön hatáskörébe, és pontosan tükrözze az összes adatgyűjtési és-feldolgozási gyakorlatot.

## <a name="application-development"></a>Alkalmazásfejlesztés 

A beléptetési folyamat megtervezése előtt gondolja át, hogy az Ön által létrehozott alkalmazás hogyan biztosíthatja a felhasználók számára az adatvédelemre vonatkozó ígéreteket. A következő javaslatok segítségével olyan regisztrációs élményt hozhat létre, amely a személyes adatok védelméhez, a felhasználók adatvédelmének kezeléséhez és az alkalmazás minden felhasználó számára elérhetővé tételének biztosításához felelős megközelítést tartalmaz.  

|Category | Javaslatok |
|---|---|
|Hardver | Vegye figyelembe a beléptetési eszköz kamerájának minőségét. |
|Ajánlott regisztrációs funkciók | A többtényezős hitelesítéssel bejelentkező lépést is tartalmaz.</br></br>Felhasználói adatok, például alias vagy azonosító szám összekapcsolása a Face API (az ismert személy azonosítója) alapján. Ez a leképezés a felhasználó beléptetésének lekéréséhez és kezeléséhez szükséges. Megjegyzés: a személy AZONOSÍTÓját az alkalmazásban titkos kulcsként kell kezelni.</br></br>Automatikus folyamat beállítása az összes beléptetési művelet törléséhez, beleértve az arc-sablonokat és a beléptetési fényképeket olyan személyek számára, akik már nem használják az Arcfelismerés technológia, például a korábbi alkalmazottak.</br></br>Kerülje az automatikus regisztrációt, mivel nem biztosítja a felhasználónak a beleegyezés beszerzéséhez ajánlott ismereteket, megértést, választási szabadságot vagy irányítást. </br></br>Kérje meg a felhasználókat, hogy mentse a regisztrációhoz használt rendszerképeket. Ez akkor hasznos, ha a modell frissítése óta új beléptetési fényképek szükségesek az új modellbe való újbóli regisztráláshoz 10 havonta. Ha az eredeti lemezképek nincsenek mentve, a felhasználóknak a beléptetési folyamat elejétől kell átesniük.</br></br>Lehetővé teszi a felhasználók számára, hogy letiltsák a fényképek tárolását a rendszeren. A választási lehetőség világosabb megadásához hozzáadhat egy második, a beléptetést kérő fényképeket a beléptetési fényképek mentéséhez. </br></br>Ha a rendszer menti a fényképeket, hozzon létre egy automatizált folyamatot az összes felhasználó újbóli regisztrálásához, ha a modell frissítése folyamatban van. Azok, akik megmentették a regisztrációs fényképeket, nem kell újból regisztrálni magukat. </br></br>Hozzon létre egy alkalmazás-szolgáltatást, amely lehetővé teszi, hogy a kijelölt rendszergazdák felülbírálják bizonyos minőségi szűrőket, ha a felhasználónak problémája van a regisztrációval. |
|Biztonság | Cognitive Services kövesse az [ajánlott eljárásokat](../cognitive-services-virtual-networks.md?tabs=portal) a felhasználói adatok inaktív és átvitel közbeni titkosításához. A következő eljárások olyan további eljárásokat mutatnak be, amelyek segíthetnek a felhasználók által a beléptetési élményben vállalt biztonsági ígéretek fenntartásában. </br></br>Tegyen biztonsági intézkedéseket annak biztosítására, hogy a regisztráció során az egyik pillanatban sem férhet hozzá a személy AZONOSÍTÓhoz. Megjegyzés: a számú personid a regisztrációs rendszer titkos kulcsaként kell kezelni. </br></br>[Szerepköralapú hozzáférés-vezérlést](../../role-based-access-control/overview.md) használhat a Cognitive Services. </br></br>Használjon jogkivonat-alapú hitelesítést és/vagy közös hozzáférési aláírásokat (SAS) a kulcsok és a titkok használatával az olyan erőforrások eléréséhez, mint például az adatbázisok. A kérelem vagy SAS-tokenek használatával korlátozott hozzáférést biztosíthat az adathoz a fiók kulcsainak veszélyeztetése nélkül, és megadhat egy lejárati időt a jogkivonatban. </br></br>Soha ne tároljon semmilyen titkot, kulcsot vagy jelszót az alkalmazásban. |
|Felhasználói adatvédelem |Számos regisztrációs lehetőséget biztosít a különböző adatvédelmi vonatkozások kezeléséhez. Ne bízzon arra, hogy az emberek személyes eszközeiket használják a beléptetésre egy Arcfelismerés rendszerbe. </br></br>Lehetővé teheti a felhasználók számára, hogy bármikor és bármilyen oknál fogva újra regisztrálják, visszavonják a beleegyezést, és törölhetik az adatait a beléptetési alkalmazásból. |
|Akadálymentesség |Kövesse a kisegítő lehetőségeket (például az [Ada](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) -t vagy a [W3C](https://www.w3.org/TR/WCAG21/)-t) annak biztosításához, hogy az alkalmazás a mobilitással vagy a vizualizációk által biztosított személyek által is használható legyen |

## <a name="next-steps"></a>További lépések  

Kövesse a [beléptetési alkalmazás létrehozása](build-enrollment-app.md) útmutatót a minta-beléptetési alkalmazás első lépéseihez. Ezután testreszabhatja vagy megírhatja saját alkalmazását a termék igényeinek megfelelően.