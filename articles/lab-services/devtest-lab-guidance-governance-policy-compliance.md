---
title: Vállalati szabályzat és megfelelőség az Azure DevTest Labs ben
description: Ez a cikk útmutatást nyújt a vállalati szabályzat és az Azure DevTest Labs-infrastruktúra megfelelőségének szabályozásáról.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 81ca2a90b1940d70e170cab3f8d18144a5d5e5a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74560507"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Az Azure DevTest Labs infrastruktúrájának irányítása – Vállalati szabályzat és megfelelőség
Ez a cikk útmutatást nyújt a vállalati szabályzat és az Azure DevTest Labs-infrastruktúra megfelelőségének szabályozásáról. 

## <a name="public-vs-private-artifact-repository"></a>Nyilvános és magánműtermék tárháza

### <a name="question"></a>Kérdés
Mikor kell egy szervezetnek nyilvános må± személyes må± összetevő-tárházat használnia a DevTest Labsben?

### <a name="answer"></a>Válasz
A [nyilvános műtermék-tárház](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) a leggyakrabban használt szoftvercsomagok kezdeti készletét biztosítja. Segít a gyors üzembe helyezésben anélkül, hogy időt kellene befektetnie a közös fejlesztői eszközök és bővítmények reprodukálására. Választhat, hogy saját privát tárház üzembe helyezése. A nyilvános és a magántártár párhuzamosan használhatja. Dönthet úgy is, hogy letiltja a nyilvános tárházat. A magántárház üzembe helyezésének kritériumait a következő kérdéseknek és szempontoknak kell vezérelniük:

- A szervezet nek van-e követelménye, hogy a DevTest Labs-ajánlatrészeként vállalati licencelt szoftverrel kell rendelkeznie? Ha a válasz igen, akkor létre kell hozni egy privát tárházat.
- A szervezet olyan egyéni szoftvert fejleszt, amely egy adott műveletet biztosít, amely a teljes kiépítési folyamat részeként szükséges? Ha a válasz igen, akkor egy privát tárházat kell telepíteni.
- Ha a szervezet cégirányítási házirendje elkülönítést igényel, és a külső adattárak nem tartoznak a szervezet közvetlen konfigurációs felügyelete alatt, egy privát må±termã©k-tárolót kell telepíteni. Ennek a folyamatnak a részeként a nyilvános tárház első példánya másolható és integrálható a magántárházba. Ezután a nyilvános tárház letiltható, így a szervezeten belül senki sem férhet hozzá. Ez a megközelítés arra kényszeríti a szervezeten belüli összes felhasználót, hogy csak egy tárház, amely a szervezet által jóváhagyott, és minimalizálja a konfigurációs eltolódás.

### <a name="single-repository-or-multiple-repositories"></a>Egyetlen adattár vagy több adattár 

### <a name="question"></a>Kérdés
Egy szervezet tervezze meg egyetlen tárházat, vagy engedélyezze a több tárházat?

### <a name="answer"></a>Válasz
A szervezet általános cégirányítási és konfigurációkezelési stratégiájának részeként azt javasoljuk, hogy használjon egy központi tárházat. Ha több adattárat használ, azok idővel a nem felügyelt szoftverek silóivá válhatnak. Egy központi tárház, több csapat is felhasználhatja a projektjeik összetevői. Kikényszeríti a szabványosítást, a biztonságot, a könnyű kezelhetőséget, és kiküszöböli az erőfeszítések megkettőzését. A központosítás részeként a következő intézkedések a hosszú távú irányítás és fenntarthatóság ajánlott gyakorlatai:

- Társítsa az Azure-tárház ugyanahhoz az Azure Active Directory-bérlőhöz, amelyet az Azure-előfizetés hitelesítéshez és engedélyezéshez használ.
- Hozzon létre egy **all DevTest Labs developers** nevű csoportot az Azure Active Directoryban, amely központilag felügyelt. Minden olyan fejlesztőt, aki hozzájárul a műtermék fejlesztéséhez, ebbe a csoportba kell helyezni.
- Ugyanaz az Azure Active Directory-csoport használható az Azure Repos tárházhoz és a laborhoz való hozzáférés biztosításához.
- Az Azure Repos,elágazás vagy elágazás kell használni egy külön fejlesztés alatt álló tárház az elsődleges éles tárház. A tartalom csak a megfelelő kódellenőrzés után kerül a főágba lekéréses kérelemmel. Miután a kódfelülvizsgáló jóváhagyja a módosítást, a főág karbantartásáért felelős vezető fejlesztő egyesíti a frissített kódot. 

## <a name="corporate-security-policies"></a>Vállalati biztonsági házirendek

### <a name="question"></a>Kérdés
Hogyan biztosíthatja egy szervezet a vállalati biztonsági házirendek érvényben lévő működését?

### <a name="answer"></a>Válasz
Egy szervezet a következő műveletekkel érheti el:

1. Átfogó biztonsági politika kidolgozása és közzététele. A szabályzat a szoftverhasználatával, felhőeszközökkel kapcsolatos elfogadható használatra vonatkozó szabályokat fogalmazza meg. Azt is meghatározza, hogy mi sérti egyértelműen a politikát. 
2. Egyéni lemezképet, egyéni összetevőket és olyan telepítési folyamatot fejleszthet ki, amely lehetővé teszi az active directoryval definiált biztonsági területen belüli vezénylést. Ez a megközelítés kényszeríti a vállalati határt, és meghatározza a közös környezetvédelmi vezérlők. Ezek a környezet elleni vezérlők a fejlesztő figyelembe veheti, ahogy azok fejlesztése és kövesse a biztonságos fejlesztési életciklus részeként a teljes folyamat. A cél az is, hogy olyan környezetet biztosítsunk, amely nem túlságosan korlátozó, ami akadályozhatja a fejlődést, hanem ésszerű ellenőrzési sorozat. A csoportházirendek a szervezeti egység (OU) tartalmazó labor virtuális gépek lehet egy részhalmaza a teljes csoport házirendek, amelyek az éles környezetben található. Alternatív megoldásként további készleteket is jelenthetnek az azonosított kockázatok megfelelő csökkentése érdekében.

## <a name="data-integrity"></a>Adatintegritás

### <a name="question"></a>Kérdés
Hogyan biztosíthatja egy szervezet az adatok integritását annak biztosítása érdekében, hogy a kapcsolathasználattal foglalkozó fejlesztők ne távolíthassák el a kódot, ne vezethessenek be rosszindulatú programokat vagy nem jóváhagyott szoftvereket?

### <a name="answer"></a>Válasz
Több rétegű vezérlés, hogy csökkentsék a külső tanácsadók, vállalkozók vagy alkalmazottak, amelyek a terés közben a DevTest Labs együttműködik. 

Amint azt korábban említettük, az első lépésnek elfogadható használatra vonatkozó szabályzatot kell kidolgoznia és meghatároznia, amely egyértelműen felvázolja a következményeket, ha valaki megsérti a szabályzatot. 

A távelérés vezérlésének első rétege a távelérési házirend alkalmazása olyan VPN-kapcsolaton keresztül, amely nem közvetlenül kapcsolódik a tesztkörnyezethez. 

A vezérlők második rétege olyan csoportházirend-objektumok alkalmazásával, amelyek megakadályozzák a távoli asztalon történő másolást és beillesztést. A hálózati házirend et úgy lehet végrehajtani, hogy a környezetből, például az FTP- és RDP-szolgáltatásokból származó kimenő szolgáltatások ne legyenek kikötve a környezetből. A felhasználó által definiált útválasztás kényszerítheti az összes Azure-hálózati forgalmat vissza a helyszíni, de az útválasztás nem tudta figyelembe venni az összes URL-eket, amelyek lehetővé teszik az adatok feltöltését, kivéve, ha egy proxyn keresztül ellenőrzik a tartalom és a munkamenetek. Nyilvános IP-k korlátozható a DevTest Labs-t támogató virtuális hálózaton belül, hogy ne engedélyezze a külső hálózati erőforrások áthidalását.

Végső soron ugyanazt a korlátozástípust kell alkalmazni az egész szervezetben, amelynek figyelembe kell vennie a cserélhető adathordozók vagy külső URL-ek minden lehetséges módszerét, amely képes elfogadni egy tartalombejegyzést. A biztonsági házirend áttekintéséhez és végrehajtásához forduljon biztonsági szakemberéhez. További javaslatokat a Microsoft Cyber Security ( microsoft kiberbiztonság ) [témakörben t.](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)


## <a name="next-steps"></a>További lépések
Lásd: [Alkalmazásáttelepítés és -integráció](devtest-lab-guidance-governance-application-migration-integration.md).
