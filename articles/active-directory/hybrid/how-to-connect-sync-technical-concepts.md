---
title: 'Azure AD Connect szinkronizálása: Technikai fogalmak | Microsoft dokumentumok'
description: Az Azure AD Connect szinkronizálásának technikai fogalmait ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ec4a6100cfbb4419d7e30f4b97589113b88939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347574"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Az Azure AD Connect szinkronizálása: technikai kulcsfogalmak
Ez a cikk az [Architektúra ismertetése](how-to-connect-sync-technical-concepts.md)című témakör összefoglalója.

Az Azure AD Connect szinkronizálása szilárd metatár-szinkronizálási platformra épül.
A következő szakaszok bemutatják a metatár-szinkronizálás fogalmait.
A MIIS, AZ ILM és a FIM szolgáltatásra építve az Azure Active Directory-szinkronizálási szolgáltatások a következő platformot biztosít az adatforrásokhoz való csatlakozáshoz, az adatforrások közötti adatok szinkronizálásához, valamint az identitások kiépítéséhez és kiépítéséhez.

![Műszaki fogalmak](./media/how-to-connect-sync-technical-concepts/scenario.png)

A következő szakaszok további részleteket tartalmaznak a FIM-szinkronizálási szolgáltatás következő szempontjairól:

* Összekötő
* Attribútumfolyam
* Összekötő tér
* Metaverse
* Kiépítés

## <a name="connector"></a>Összekötő
A csatlakoztatott könyvtárral való kommunikációhoz használt kódmodulokat összekötőknek (korábbi nevén felügyeleti ügynököknek (MA)-nak nevezzük).

Ezek az Azure AD Connect szinkronizálást futtató számítógépre vannak telepítve. Az összekötők biztosítják az ügynök nélküli képes beszélgetni távoli rendszerprotokollok használatával, ahelyett, hogy a speciális ügynökök telepítésére támaszkodna. Ez azt jelenti, hogy csökken a kockázat és a telepítési idő, különösen a kritikus alkalmazások és rendszerek kezelése során.

A fenti képen a csatlakozó egyet jelent a csatlakozó tér, de magában foglalja az összes kommunikációt a külső rendszer.

Az összekötő felelős a rendszer összes importálási és exportálási funkciójáért, és leszabadítja a fejlesztőket attól, hogy megértsék, hogyan csatlakozhatnak az egyes rendszerhez natív módon, amikor deklaratív kiépítéssel testreszabják az adatátalakításokat.

Az import és az exportálás csak ütemezett időpontban történik, ami lehetővé teszi a rendszeren belül bekövetkező változások tól való további szigetelést, mivel a változások nem terjednek át automatikusan a csatlakoztatott adatforrásra. Ezenkívül a fejlesztők saját összekötőket is létrehozhatnak gyakorlatilag bármilyen adatforráshoz való csatlakozáshoz.

## <a name="attribute-flow"></a>Attribútumfolyam
A metaverzum a szomszédos összekötőterekösszes illesztett identitásának összevont nézete. A fenti ábrán az attribútumfolyamatot nyílhegyekkel ellátott vonalak ábrázolják mind a bejövő, mind a kimenő folyamathoz. Az attribútumfolyamat az adatok egyik rendszerből a másikba történő másolásának vagy átalakításának folyamata, és az összes attribútumfolyam (bejövő vagy kimenő).

Attribútumfolyam az összekötő tér és a metaverzum kétirányú között történik, amikor a szinkronizálási (teljes vagy delta) műveletek futtatására vannak ütemezve.

Az attribútumfolyamat csak akkor fordul elő, ha ezek a szinkronizálások futnak. Az attribútumfolyamatok a szinkronizálási szabályokban vannak definiálva. Ezek lehetnek bejövő (ISR a fenti képen) vagy kimenő (OSR a fenti képen).

## <a name="connected-system"></a>Csatlakoztatott rendszer
Csatlakoztatott rendszer (más néven csatlakoztatott könyvtár) hivatkozik a távoli rendszer Azure AD Connect szinkronizálás csatlakozik, és az identitásadatok olvasása és írása identitásadatokat, és onnan.

## <a name="connector-space"></a>Összekötő tér
Minden csatlakoztatott adatforrás az összekötő térben lévő objektumok és attribútumok szűrt részhalmazaként jelenik meg.
Ez lehetővé teszi, hogy a szinkronizálási szolgáltatás helyileg működjön anélkül, hogy kapcsolatba kellene lépnie a távoli rendszerrel az objektumok szinkronizálásakor, és csak az importálásra és exportálásra korlátozza az interakciót.

Ha az adatforrás és az összekötő képes a változások listájának (különbözeti importálás) megadására, akkor a működési hatékonyság drámaian megnő, mivel csak a legutóbbi lekérdezési ciklus óta bekövetkezett változások. Az összekötő tér szigeteli a csatlakoztatott adatforrást a módosítások automatikus propagálása megkövetelve, hogy az összekötő ütemezése import és exportálás. Ez a hozzáadott biztosítás nyugalmat biztosít a következő frissítés tesztelése, előnézete vagy megerősítése során.

## <a name="metaverse"></a>Metaverse
A metaverzum a szomszédos összekötőterekösszes illesztett identitásának összevont nézete.

Mivel az identitások össze vannak kapcsolva, és a különböző attribútumokhoz importfolyamat-leképezések en keresztül van hozzárendelve a jogosultság, a központi metaverzum-objektum elkezdi összesíteni a több rendszerből származó információkat. Ebből az objektumattribútum-folyamatból a leképezések adatokat szállítanak a kimenő rendszerekhez.

Az objektumok akkor jönnek létre, amikor egy mérvadó rendszer kivetíti őket a metaverzumba. Amint az összes kapcsolat törlődik, a metaverzum-objektum törlődik.

A metaverzumban lévő objektumok nem szerkeszthetők közvetlenül. Az objektumban lévő összes adatot attribútumfolyamaton keresztül kell hozzátenni. A metaverzum minden összekötő térben állandó összekötőket tart fenn. Ezek az összekötők nem igényelnek újraértékelést minden szinkronizálási futtatáshoz. Ez azt jelenti, hogy az Azure AD Connect szinkronizálási nem kell minden alkalommal megtalálni a megfelelő távoli objektumot. Így nincs szükség költséges ügynökökre, hogy megakadályozzák az olyan attribútumok módosítását, amelyek általában felelősek az objektumok korrelációért.

Amikor olyan új adatforrásokat fedez fel, amelyek már meglévő, már kezelhető objektumokkal rendelkeznek, az Azure AD Connect szinkronizálása egy csatlakozási szabálynak nevezett folyamatot használ a potenciális jelöltek kiértékelésére, amelyekkel kapcsolatot hozhat létre.
A kapcsolat létrehozása után ez a kiértékelés nem fordul elő újra, és normál attribútumfolyamat fordulhat elő a távoli csatlakoztatott adatforrás és a metaverzum között.

## <a name="provisioning"></a>Kiépítés
Amikor egy mérvadó forrás egy új objektumot vetít ki a metaverzumba, egy másik összekötőben új összekötő térobjektum hozható létre, amely egy alsóbb rétegbeli csatlakoztatott adatforrást jelöl.

Ez eredendően kapcsolatot hoz létre, és az attribútumfolyamat kétirányúan haladhat tovább.

Amikor egy szabály azt állapítja meg, hogy egy új összekötő tér objektumot kell létrehozni, azt kiépítésnek nevezzük. Mivel azonban ez a művelet csak az összekötő térben történik, az exportálás végrehajtásáig nem kerül át a csatlakoztatott adatforrásba.

## <a name="additional-resources"></a>További források
* [Azure AD Connect Sync: Szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
