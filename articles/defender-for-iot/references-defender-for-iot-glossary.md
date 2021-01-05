---
title: Defender a IoT-hez – Szószedet
description: Ez a Szószedet a fontos Defender rövid leírását tartalmazza a IoT platform használati feltételeit és fogalmait illetően.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 7896237b1f9e9e66659532422efb3449c41ede11
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845281"
---
# <a name="defender-for-iot-glossary"></a>Defender a IoT-hez – Szószedet

Ez a Szószedet röviden ismerteti az Azure Defender for IoT platform fontos használati feltételeit és fogalmait. Kattintson a **További tudnivalók** hivatkozásra, hogy a kapcsolódó kifejezésekhez lépjen a szószedetben. Ez segít a termék-eszközök gyors megismerésében és használatában.

## <a name="a"></a>A

| Időszak | Leírás | További információ |
|--|--|--|
| **hozzáférési csoport** | A hozzáférési csoport szabályainak létrehozásával támogatja a nagyméretű szervezetek felhasználói hozzáférési követelményeit.<br /><br />A szabályok segítségével szabályozhatja a Defender IoT-hozzáférését a helyszíni felügyeleti konzolhoz adott felhasználói szerepkörökhöz a megfelelő üzleti egységekben, régiókban, helyeken és zónákban.<br /><br />Tegyük fel például, hogy a biztonsági elemzők egy Active Directory csoportból érik el a Nyugat-európai autóipari adatok elérését, de megakadályozzák az adatok elérését Afrikában. | **helyszíni felügyeleti konzol <br /> <br /> üzleti egysége** |
| **hozzáférési jogkivonatok** | Hozzáférési jogkivonatok előállítása a Defender IoT REST APIhoz való hozzáféréséhez. | **API** |
| **Riasztási esemény nyugtázása** | Utasítsa a Defendert a IoT, hogy az észlelt eseményhez egyszer elrejtse a riasztást. A riasztás újra aktiválódik, ha az eseményt újra észleli a rendszer. | **riasztás – riasztási <br /> <br /> esemény <br /> <br /> elnémításának riasztási eseménye** |
| **riasztás** | Üzenet arról, hogy a IoT-kezelő Defender a hitelesítő hálózati viselkedéstől, a hálózati rendellenességektől, illetve a gyanús hálózati tevékenységtől és forgalomtól való eltérésekkel kapcsolatos. | **szabály <br /> <br /> kizárása a szabályok kizárására szabályrendszer <br /> <br /> értesítései** |
| **riasztási Megjegyzés** | Megjegyzések, amelyeket a biztonsági elemzők és a rendszergazdák a riasztási üzenetekben tesznek elérhetővé. Egy riasztási Megjegyzés például megadhatja az eseményre vonatkozó kockázatcsökkentő műveletek, illetve az eseménnyel kapcsolatban álló személyek nevét.<br /><br />A riasztásokat véleményező felhasználók kiválaszthatják az esemény állapotát legjobban tükröző észrevételeket vagy megjegyzéseket, illetve a riasztás kivizsgálásához szükséges lépéseket. | **riasztás** |
| **anomália motor** | A IoT motorhoz tartozó Defender, amely észleli a gépek közötti szokatlan kommunikációt és viselkedést. Előfordulhat például, hogy a motor a túlzott SMB-bejelentkezési kísérletek észlelésére is képes. Az anomáliák riasztása akkor aktiválódik, ha ezek az események észlelhetők. | **Defender a IoT-motorokhoz** |
| **API** | Lehetővé teszi, hogy a külső rendszerek hozzáférhessenek a Defender által felderített adatokhoz a IoT számára, és műveleteket hajtsanak végre az SSL-kapcsolatok külső REST API használatával. | **hozzáférési jogkivonatok** |
| **támadási vektor jelentés** | A kihasználható végpontok sebezhetőségi láncának valós idejű grafikus ábrázolása.<br /><br />A jelentések lehetővé teszik a kockázatcsökkentő tevékenységek hatásának kiértékelését a támadási sorozatban. Kiértékelheti például, hogy a rendszerfrissítés megszakítja-e a támadó elérési útját a támadási lánc megszakadásával, vagy ha egy másik támadási útvonal marad. Ez rangsorolja a Szervizelési és a kockázatcsökkentő tevékenységeket. | **kockázatértékelési jelentés** |

## <a name="b"></a>B

| Időszak | Leírás | További információ |
|--|--|--|
| **üzleti egység** | Az üzleti tevékenység logikai szervezete adott iparágak szerint.<br /><br />Egy üveg-és műanyag-gyárakat tartalmazó globális vállalat például két különböző üzleti egységként kezelhető. A Defender hozzáférését a IoT-felhasználók meghatározott üzleti egységekre is szabályozhatja. | **helyszíni felügyeleti konzol <br /> <br /> hozzáférési csoportjának <br /> <br /> helye <br /> <br /> zóna** |
| **alapkonfiguráció** | Jóváhagyott hálózati forgalom, protokollok, parancsok és eszközök. A IoT Defender a hálózati alaptervtől való eltéréseket azonosítja. Az adatbányászati jelentések létrehozásával megtekintheti a jóváhagyott alapterv-forgalmat. | **adatbányászati <br /> <br /> tanulási mód** |

## <a name="c"></a>C#

| Időszak | Leírás | További információ |
|--|--|--|
| **helyszíni felügyeleti konzol** | A helyszíni felügyeleti konzol az eszközök és fenyegetések központi megtekintését és kezelését biztosítja, amelyeket a Defender a IoT-érzékelő üzembe helyezése során észlel a szervezetében. | **Defender a IoT platform- <br /> <br /> érzékelőhöz** |
| **Parancssori felületi parancsok** | Parancssori felület (CLI) beállításai a Defender számára a IoT-rendszergazda felhasználók számára. A CLI-parancsok olyan funkciókhoz érhetők el, amelyek nem érhetők el a Defender for IoT Consoles szolgáltatásban. | - |


## <a name="d"></a>T

| Időszak | Leírás | További információ |
|--|--|--|
| **adatbányászat** | Átfogó és részletes jelentések készítése a hálózati eszközökről:<br /><br />- **SoC incidens válasza**: a valós idejű jelentések segítenek az incidensek azonnali megválaszolásában. Egy jelentés például listázhat olyan eszközöket, amelyekhez szükség lehet a javításra.<br /><br />- **kriminalisztika**: jelentések a korábbi adatelemzési jelentések alapján.<br /><br />- **Informatikai hálózat integritása**: jelentések, amelyek segítenek a teljes hálózati biztonság javításában. Egy jelentés például a gyenge hitelesítési hitelesítő adatokkal rendelkező eszközöket listázhatja.<br /><br />- **láthatóság**: az összes lekérdezési elemre kiterjedő jelentések a hálózat összes alapkonfigurációjának megtekintéséhez.<br /><br />Mentse az adatbányászati jelentéseket a csak olvasási jogosultsággal rendelkező felhasználók megtekintéséhez. | **alapkonfiguráció- <br /> <br /> jelentések** |
| **Defender a IoT-motorokhoz** | Az önfejlesztő elemzési motorok a IoT Defenderban nem szükségesek az aláírások frissítéséhez vagy a szabályok definiálásához. A motorok az IC-specifikus viselkedés-elemzést és adatelemzést használják a rendellenességek, kártevők, működési problémák, protokollok megsértése és a kiindulási hálózati tevékenységtől való eltérések folyamatos elemzéséhez.<br /><br />Ha egy motor eltérést észlel, riasztást vált ki. A riasztásokat a **riasztások** képernyőről vagy egy Siem-ből lehet megtekinteni és kezelni. | **riasztás** |
| **Defender a IoT platformhoz** | A Defender for IoT megoldás a IoT érzékelőkre és a helyszíni felügyeleti konzolra van telepítve. | **helyszíni érzékelő <br /> <br /> felügyeleti konzol** |
| **eszköz térképe** | A IoT Defender által észlelt hálózati eszközök grafikus ábrázolása. Megjeleníti az eszközök közötti kapcsolatokat és az egyes eszközökre vonatkozó információkat. A térképen a következőt használhatja:<br /><br />– A kritikus eszközök adatainak beolvasása és vezérlése.<br /><br />– Hálózati szeletek elemzése.<br /><br />– Az eszköz adatainak és összegzésének exportálása. | **Purdue-réteg csoport** |
| **eszköz leltározása – érzékelő** | Az eszközök leltára a Defender által a IoT számára észlelt eszközök széles körét jeleníti meg. A következő lehetőségek érhetők el:<br /><br />– Megjelenített adatok szűrése.<br /><br />– Exportálja ezt az információt egy CSV-fájlba.<br /><br />– Importálja a Windows beállításjegyzékének részleteit. | **csoportos eszköz leltározása – helyszíni felügyeleti konzol** |
| **eszközök leltározása – helyszíni felügyeleti konzol** | A csatlakoztatott érzékelőkről származó információkat a helyszíni felügyeleti konzolról lehet megtekinteni az eszközök leltárában. Ez lehetővé teszi a helyszíni felügyeleti konzol számára az összes hálózati információ átfogó áttekintését. | **eszközök leltára – érzékelő <br /> <br /> eszköz leltározása – adatintegrátor** |
| **eszközök leltározása – adatintegrátor** | A helyszíni felügyeleti konzol adatintegrációs funkciói lehetővé teszik, hogy az eszközök leltárában lévő adatokat más vállalati erőforrásokból származó adatokkal fejlessze. Az erőforrások például CMDBs, DNS, tűzfalak és webes API-k. | **eszközök leltározása – helyszíni felügyeleti konzol** |

## <a name="e"></a>E

| Időszak | Leírás | További információ |
|--|--|--|
| **vállalati nézet** | Egy globális Térkép, amely olyan üzleti egységeket, webhelyeket és zónákat mutat be, ahol a Defender for IoT érzékelők telepítve vannak. Megtekintheti a kártékony riasztások földrajzi elhelyezkedését, az operatív riasztásokat és egyebeket. | **üzleti egység <br /> <br /> helyének <br /> <br /> zónája** |
| **esemény idővonala** | A hálózaton észlelt tevékenység ütemezése, beleértve a következőket:<br /><br />– Aktivált riasztások.<br /><br />– Hálózati események (tájékoztató).<br /><br />– Olyan felhasználói műveletek, mint például a bejelentkezés, a felhasználók törlése és a felhasználók létrehozása, valamint a riasztások kezelési műveletei, például az Elnémítás, a tanulás és a nyugtázás. Elérhető az érzékelők konzolján. | - |
| **kizárási szabály** | Utasítsa a Defendert a IoT, hogy figyelmen kívül hagyja a riasztási eseményindítókat az időszak, az eszköz címe és a riasztás neve vagy egy adott érzékelő alapján.<br /><br />Ha például biztos lehet abban, hogy egy adott érzékelő által figyelt összes eszközön a 6:30 és a 10:15 közötti karbantartási eljárás fut, akkor beállíthat egy kizárási szabályt, amely azt jelzi, hogy az érzékelő nem küld riasztást az előre meghatározott időszakban. | **riasztás <br /> <br /> némítására figyelmeztető esemény** |

## <a name="f"></a>F

| Időszak | Leírás | További információ |
|--|--|--|
| **továbbítási szabály** | A továbbítási szabályok utasítja a Defendert, hogy a IoT küldjön riasztási információkat a partner-szállítók vagy rendszerek számára.<br /><br />Küldhet például riasztási adatokat egy splunk-kiszolgálónak vagy egy syslog-kiszolgálónak. | **riasztás** |

## <a name="g"></a>G

| Időszak | Leírás | További információ |
|--|--|--|
| **hozzáadása** | Meghatározott attribútumokat (például a programozási tevékenységet vagy egy adott alhálózaton található eszközöket) tartalmazó eszközök előre definiált vagy egyedi csoportjai. Csoportok segítségével megtekintheti az eszközöket, és elemezheti az eszközöket a Defenderben a IoT.<br /><br />A csoportok megtekinthetők és létrehozhatók az eszköz-hozzárendelésből és az eszközök leltározásával. | **eszköz-hozzárendelési <br /> <br /> eszközök leltára** |

## <a name="h"></a>H

| Időszak | Leírás | További információ |
|--|--|--|
| **Horizonton nyitott fejlesztési környezet** | Biztonságos IoT és ICS-eszközök, amelyek olyan tulajdonosi és egyéni protokollokat vagy protokollokat futtatnak, amelyek bármely standardtól eltérnek. A Horizon Open Development Environment (ODE) SDK használatával olyan nem ágazati beépülő modulokat fejleszthet, amelyek a megadott protokollok alapján dekódolják a hálózati forgalmat. A IoT Services Defender a teljes monitorozást, riasztást és jelentéskészítést biztosító forgalmat elemzi.<br /><br />Horizont használata:<br /><br />- **Kiterjesztheti** a láthatóságot és a vezérlést anélkül, hogy frissítenie kell a Defendert a IoT platform verzióira.<br /><br />- **Gondoskodjon** arról, hogy a helyszínen külső beépülő modulként fejlessze a védett adatokat.<br /><br />- A riasztások, események és protokollok paramétereinek **honosítása** .<br /><br />Részletekért forduljon az ügyfél sikerességi képviselőjéhez. | **protokoll támogatásának <br /> <br /> honosítása** |
| **Horizon egyéni riasztás** | A riasztások kezelése a vállalaton belül egyéni riasztások kiváltásával bármely protokollhoz (a Horizon-keretrendszer forgalmának megléte alapján).<br /><br />Ezek a riasztások a következő információk közlésére használhatók:<br /><br />– A forgalom észlelése protokollok és alapul szolgáló protokollok alapján egy saját horizontú beépülő modulban.<br /><br />-A protokoll mezőinek kombinációja az összes protokoll rétegből. | **protokollok támogatása** |

## <a name="i"></a>I

| Időszak | Leírás | További információ |
|--|--|--|
| **integrációk** | Bontsa ki a Defender for IoT képességeit, ha megosztja az eszköz adatait a partneri rendszerekkel. A szervezetek a korábban silózott biztonsági, NAC-, incidens-felügyeleti és eszközkezelés-megoldásokkal tudják felgyorsítani a rendszerszintű válaszokat, és gyorsabban csökkentik a kockázatokat. | **továbbítási szabály** |
| **belső alhálózat** | A Defender által a IoT számára meghatározott alhálózati konfigurációk. Bizonyos esetekben, például belső tartományként nyilvános tartományokat használó környezetekben a Defender a IoT az összes alhálózat belső alhálózatként való feloldására is utasíthatja. Az alhálózatok a térképen és a IoT különböző Defender-jelentésekben jelennek meg. | **alhálózatok** |

## <a name="l"></a>L

| Időszak | Leírás | További információ |
|--|--|--|
| **Riasztási esemény megismerése** | Utasítsa a Defendert a IoT, hogy engedélyezze a riasztási eseményekben észlelt forgalmat. | **riasztás nyugtázása riasztási <br /> <br /> esemény némító riasztási <br /> <br /> eseménye** |
| **tanulási mód** | Az a mód, amelyet a IoT Defender a hálózati tevékenység megismerésére használ. Ez a tevékenység lesz a hálózati alapterv. A IoT Defender a telepítés után előre meghatározott időszakban marad a módban. Az a tevékenység, amely eltér a megtanult tevékenységtől, miután ez az időszak elindítja a Defender IoT-riasztásokat. | **Intelligens informatikai alapkonfiguráció <br /> <br />** |
| **honosítási** | A riasztások, események és protokoll paramétereinek honosítása a horizont által fejlesztett, nem ágazati beépülő modulok esetében. | **Horizonton nyitott fejlesztési környezet** |

## <a name="m"></a>M

| Időszak | Leírás | További információ |
|--|--|--|
| **Riasztási esemény némítása** | Utasítsa a Defendert a IoT, hogy folyamatosan figyelmen kívül hagyja a tevékenységeket azonos eszközökkel és hasonló adatforgalommal. | **riasztás <br /> <br /> kizárási szabálya – <br /> <br /> riasztási esemény – <br /> <br /> Learn riasztási esemény** |

## <a name="n"></a>N

| Időszak | Leírás | További információ |
|--|--|--|
| **értesítések** | Információk a hálózati változásokról vagy a megoldatlan eszközök tulajdonságairól. Lehetőség van az eszköz-és a hálózati adatok frissítésére az észlelt új adatokkal. Az értesítésekre adott válaszok gazdagítják az eszközök leltárát, térképét és különböző jelentéseit. Elérhető az érzékelők konzolján. | **riasztási <br /> <br /> rendszer értesítései** |

## <a name="o"></a>O

| Időszak | Leírás | További információ |
|--|--|--|
| **működési riasztás** | Az operatív hálózati problémákkal foglalkozó riasztások, például egy gyanús eszköz, amely a hálózatról leválasztható. | **riasztások <br /> <br /> biztonsági riasztása** |

## <a name="p"></a>P

| Időszak | Leírás | További információ |
|--|--|--|
| **Purdue-réteg** | Megjeleníti a térképen egy tipikus internetkapcsolat fő összetevőinek összekapcsolását és függőségeit. |  |
| **protokollok támogatása** | A beágyazott protokoll támogatásán kívül a IoT és az INTERNETKAPCSOLATtal rendelkező, saját és egyéni protokollokat futtató eszközök, illetve bármely standardtól eltérő protokollok is biztonságossá tehetők a Horizon Open Development Environment SDK használatával. | **Horizonton nyitott fejlesztési környezet** |

## <a name="r"></a>R

| Időszak | Leírás | További információ |
|--|--|--|
| **régió** | Egy globális szervezet logikai részlege földrajzi régiókba. Ilyenek például a következők: Észak-Amerika, Nyugat-Európa és Kelet-Európa.<br /><br />Előfordulhat, hogy a Észak-Amerika különböző üzleti egységekből származó gyárakat tartalmaz. | **hozzáférési csoport <br /> <br /> üzleti egységének helyszíni <br /> <br /> felügyeleti konzoljának <br /> <br /> helye <br /> <br /> zóna** |
| **jelentések** | A jelentések az adatbányászati lekérdezés eredményei által generált információkat tükrözik. Ez magában foglalja az adatbányászat alapértelmezett eredményeit is, amelyek a **jelentések** nézetben érhetők el. A rendszergazdák és a biztonsági elemzők egyéni adatbányászati lekérdezéseket is létrehozhatnak, és jelentésekként menthetik őket. Ezek a jelentések a csak olvasási jogosultsággal rendelkező felhasználók számára is elérhetők lesznek. | **adatbányászat** |
| **kockázatértékelési jelentés** | A kockázatértékelési jelentéskészítés lehetővé teszi, hogy az egyes hálózati eszközök biztonsági pontszámát, valamint egy általános hálózati biztonsági pontszámot készítsen. A teljes pontszám a 100 százalékos biztonság százalékos arányát jelöli. A jelentés olyan kockázatcsökkentő javaslatokat tartalmaz, amelyek segítségével javíthatja a jelenlegi biztonsági pontszámát. | - |

## <a name="s"></a>S

| Időszak | Leírás | További információ |
|--|--|--|
| **biztonsági riasztás** | A biztonsági problémákkal foglalkozó riasztások, például a túlzott SMB-bejelentkezési kísérletek vagy a kártevők észlelése. | **riasztás <br /> <br /> működési riasztása** |
| **szelektív szondázás** | A IoT Defender passzívan vizsgálja meg az IT-t és az OT, és észleli az eszközökre, azok attribútumaira, viselkedésére és egyéb adataira vonatkozó információkat. Bizonyos esetekben előfordulhat, hogy egyes információk nem láthatók a passzív hálózati elemzésekben.<br /><br />Ha ez történik, a IoT-ben a biztonságos, részletesen megjelenő szondázás-eszközöket használhatja a korábban nem elérhető eszközökkel kapcsolatos fontos információk megtalálásához. | - |
| **érzékelő** | Az a fizikai vagy virtuális gép, amelyen a Defender for IoT platform telepítve van. | **helyszíni felügyeleti konzol** |
| **hely** | Egy gyár vagy más entitás helye. A helynek tartalmaznia kell egy zónát vagy több olyan zónát, amelyben az érzékelő telepítve van. | **zóna** |
| **Hely kezelése** | A helyszíni felügyeleti konzol lehetőséget, amely lehetővé teszi a vállalati érzékelők kezelését. | - |
| **Intelligens informatikai tanulás** | A tanulási időszak befejezése és a tanulási mód letiltását követően a Defender a IoT esetében szokatlanul magas szintű alapszintű változásokat érzékel, amelyek a normál informatikai tevékenységek, például a DNS és a HTTP-kérések eredménye. Előfordulhat, hogy ez a forgalom szükségtelen házirend-megsértési riasztásokat és rendszerértesítéseket vált ki. A riasztások és értesítések csökkentése érdekében engedélyezheti az intelligens IT-tanulást. | **képzési mód alapkonfigurációja <br /> <br />** |
| **alhálózatok** | Ha engedélyezni szeretné a fókuszt az OT-eszközökön, az IT-eszközöket a rendszer automatikusan összesíti az eszköz térképén lévő alhálózat alapján. Mindegyik alhálózat egyetlen entitásként jelenik meg a térképen, beleértve egy interaktív összecsukási vagy bővítési képességet, amely az IT-alhálózatra és a háttérre koncentrál. | **eszköz térképe** |
| **Rendszerértesítések** | A helyszíni felügyeleti konzolról érkező értesítések újraosztályozása:<br /><br />– Érzékelő csatlakoztatási állapota.<br /><br />– Távoli biztonsági mentés hibái. | **értesítések <br /> <br /> riasztása** |

## <a name="z"></a>Z

| Időszak | Leírás | További információ |
|--|--|--|
| **zóna** | Egy hely egy olyan helyen belül, amelyben az érzékelő vagy érzékelők telepítve vannak. | **hely <br /> <br /> üzleti egység <br /> <br /> régiója** |
