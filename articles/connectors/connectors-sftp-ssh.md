---
title: Csatlakozhat SFTP-kiszolgáló az ssh-val – Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat, amelyek figyelése, létrehozása, kezelése, küldése és fogadása fájlok SFTP-kiszolgálóra az SSH és az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
tags: connectors
ms.date: 01/15/2019
ms.openlocfilehash: 660d785baf12052bddf5206d8641116c9ac606aa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575096"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Figyelheti, létrehozása és kezelése az SFTP-fájlok az SSH és az Azure Logic Apps használatával

Automatizálhatja a feladatokat, amelyek figyelése, létrehozása, küldése és fájlok fogad a egy [biztonságos fájl Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) használatával a [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokoll, alkalmazásfejlesztést és -integráció automatizálása a munkafolyamatok Azure Logic Apps és az SFTP-SSH-összekötő használatával. Az SFTP egy hálózati protokoll, amely hozzá a fájlhoz, a fájlátvitel és a fájlkiszolgáló felügyelete bármelyik megbízható adatfolyam keresztül. Az alábbiakban néhány példa automatizálható feladatokra: 

* Ez a figyelő fájlok hozzáadásakor vagy módosítani.
* Első, létrehozása, másolja, átnevezése, frissítse a listát, és fájlok törlése.
* Mappák létrehozása.
* Fájl tartalom és metaadatok beolvasása.
* Mappák archívumok kibontása.

Eseményindítókat, amelyek az SFTP-kiszolgálón lévő események figyelésére és egyéb műveletek számára elérhetővé tenni a kimeneti is használhatja. Műveleteket, amelyeket az SFTP-kiszolgáló a különböző feladatok elvégzésére is használhatja. Egyéb műveletek a logikai alkalmazás kimenetét a SFTP-műveletek használata is rendelkezhet. Például ha rendszeresen kérnek le fájlok az SFTP-kiszolgálóról, elküldheti e-mailes riasztásokhoz ezeket a fájlokat és a tartalom az Office 365 Outlook-összekötőt vagy Outlook.com-összekötő használatával.
Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Korlátok

* SFTP-SSH műveletek olvashatja vagy írhatja a fájlokat, amelyek *1 GB-os vagy kisebb* , mivel kezeli az adatok *50 MB-os darab*, nem 1 GB-os darab.

* A fájlok *1 GB-nál nagyobb*, műveleteket használhatja [üzenet darabolás](../logic-apps/logic-apps-handle-large-messages.md). Az SFTP-SSH-eseményindítók jelenleg nem támogatja a darabolás.

További különbségekről, tekintse át a [hasonlítsa össze az SFTP-SSH és az SFTP](#comparison) újabb, a következő szakaszban.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Compare SFTP-SSH versus SFTP

Az alábbiakban az egyéb az SFTP-SSH-összekötő és az SFTP-összekötővel, ahol az SFTP-SSH-összekötő rendelkezik-e ezek a képességek közötti fő különbségeket:

* Használja a <a href="https://github.com/sshnet/SSH.NET" target="_blank"> **SSH.NET** </a> könyvtár, amely egy nyílt forráskódú Secure Shell (SSH) kódtár, amely támogatja a .NET.

  > [!NOTE]
  >
  > Az SFTP-SSH összekötő támogatja *csak* a titkos kulcsok, formátumok, algoritmusokat és ujjlenyomatok:
  >
  > * **Titkos kulcs formátumok**: RSA (egyeztetéséhez a Rivest-Shamir Adleman) és a DSA (Digital Signature Algorithm) kulcsot az OpenSSH-és a ssh.com
  > * **Titkosítási algoritmusok**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC, and AES-256-CBC
  > * **Ujjlenyomattal történő**: MD5

* Műveletek olvashatja vagy írhatja fájlok *legfeljebb 1 GB-os* az SFTP-összekötővel, de leíró adatok darabokban 50 MB-ot, nem 1 GB-os darab képest. 1 GB-nál nagyobb méretű fájlokhoz, műveletek is használhatja [üzenet darabolás](../logic-apps/logic-apps-handle-large-messages.md). Az SFTP-SSH-eseményindítók jelenleg nem támogatja a darabolás.

* Itt a **mappa létrehozása** művelet, amely egy mappát hoz létre az SFTP-kiszolgáló a megadott elérési úton.

* Itt a **fájl átnevezése tevékenység** művelet, amely az SFTP-kiszolgáló egy fájlt nevez át.

* A kapcsolat az SFTP-kiszolgáló gyorsítótárazza *a legfeljebb 1 óra*, amely javítja a teljesítményt, és csökkenti a kiszolgálóhoz való kapcsolódás kísérletek számát. Ez az időtartam beállítása a gyorsítótárazási viselkedésének, szerkessze a <a href="https://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank"> **ClientAliveInterval** </a> tulajdonság frissítése az SSH-konfigurációja az SFTP-kiszolgálón.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* Az SFTP címet és a fiók hitelesítő adatait, lehetővé teszik a logikai alkalmazás az SFTP-fiók eléréséhez. Titkos SSH-kulcs és titkos kulcs jelszavát SSH való hozzáférés is szükséges. 

  > [!IMPORTANT]
  >
  > Az SFTP-SSH összekötő támogatja *csak* a titkos kulcs formátumok, algoritmusokat és ujjlenyomatok:
  > 
  > * **Titkos kulcs formátumok**: RSA (egyeztetéséhez a Rivest-Shamir Adleman) és a DSA (Digital Signature Algorithm) kulcsot az OpenSSH-és a ssh.com
  > * **Titkosítási algoritmusok**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC, and AES-256-CBC
  > * **Ujjlenyomattal történő**: MD5
  >
  > Miután hozzáadta az SFTP-SSH eseményindítót vagy műveletet hoz létre a logikai alkalmazás, amikor szüksége az SFTP-kiszolgáló kapcsolati adatainak megadása. 
  > Ha a titkos SSH-kulcsot használ, győződjön meg arról, hogy ***másolási*** a kulcs az SSH megszerezné a titkos kulcsot, és ***illessze be*** a kulcs a kapcsolat részletek ***manuálisan nem adja meg vagy módosítsa a kulcsot***, így előfordulhat, hogy a kapcsolat sikertelen lesz. 
  > További információkért lásd az ebben a cikkben a későbbi lépésekben.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni az SFTP-fiókjához. Szeretne kezdeni egy SFTP-SSH-triggert, [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az SFTP-SSH művelet használatához indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-sftp-with-ssh"></a>Csatlakozhat SFTP az ssh-val

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Üres logic Apps, a Keresés mezőbe írja be a "sftp ssh" szűrőként. Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

   – vagy –

   Meglévő logic Apps alkalmazások, az utolsó lépés, adjon hozzá egy műveletet, amelyre a válasszon **új lépés**. 
   A Keresés mezőbe írja be a "sftp ssh" szűrőként. 
   Műveletek listája alatt válassza ki a kívánt művelet.

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. Adja meg a szükséges adatokat a kapcsolatot.

   > [!IMPORTANT] 
   >
   > Amikor beírja a titkos SSH-kulcs a a **titkos SSH-kulcs** tulajdonságot használja, kövesse az alábbi kiegészítő lépéseket, és segít abban, hogy a teljes és megfelelő érték a tulajdonság adja meg. 
   > Érvénytelen a kulcs hatására a kapcsolat sikertelen lesz.
   
   Bár használhatja bármilyen szövegszerkesztővel, az alábbiakban minta lépések azt mutatják be, hogyan kell megfelelően másolja és illessze be a kulcsot a Notepad.exe használatával például.
    
   1. Egy szövegszerkesztőben nyissa meg az SSH megszerezné a titkos kulcsot. 
   Ezeket a lépéseket a Jegyzettömb használja példaként.

   1. A Jegyzettömb **szerkesztése** menüjében válassza **válassza ki az összes**.

   1. Válassza ki **szerkesztése** > **másolási**.

   1. Az SFTP-SSH-eseményindító vagy hozzáadott művelet, illessze be a *teljes* kulcs másolt a **titkos SSH-kulcs** tulajdonság, amely támogatja a több sort. 
   ***Győződjön meg arról, hogy illessze be*** a kulcsot. ***Manuálisan nem adja meg vagy módosítsa a kulcsot***.

1. Ha befejezte a kapcsolati adatok megadása, válassza a **létrehozás**.

1. Most adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="trigger-limits"></a>Eseményindító-korlátok

Az SFTP-SSH eseményindítók működnek az SFTP-fájlrendszer lekérdezésével, és keres olyan fájlt, amely a legutóbbi lekérdezés óta módosult. Egyes eszközök segítségével történő küldés időbélyegzője legyen megőrzése, ha módosítja a fájlokat. Ezekben az esetekben kell letiltani ezt a funkciót, így az eseményindító is dolgozhat. Az alábbiakban néhány gyakori beállítások:

| Az SFTP-ügyfél | Műveletek | 
|-------------|--------| 
| Winscp | Lépjen a **beállítások** > **beállítások** > **Transfer** > **szerkesztése**  >  **Időbélyeg megőrzése** > **letiltása** |
| FileZilla | Lépjen a **Transfer** > **időbélyegeket átvitt fájlok megőrzéséhez** > **letiltása** | 
||| 

Ha az eseményindító egy új fájlt talál, a trigger ellenőrzi, hogy az új fájl teljes és részlegesen írásos. Például egy fájl előfordulhat módosítások folyamatban, amikor a trigger ellenőriz a fájlkiszolgálón. Részlegesen írásos fájl visszaadó elkerüléséhez az eseményindító feljegyzi az időbélyeg, amely rendelkezik a legutóbbi módosítások, de nem ad vissza a fájlt közvetlenül a fájl. A trigger a fájl adja vissza, csak akkor, ha a kiszolgáló ismét lekérdezés. Egyes esetekben ez a viselkedés, amely legfeljebb kétszer az eseményindító a lekérdezési időköz késleltetés okozhatja. 

Fájl tartalmának kérésekor eseményindítók nem kap fájlok 50 MB-nál nagyobb. 50 MB-nál nagyobb fájlok lekéréséhez kövesse az ezt a mintát: 

* Használjon egy eseményindítót, amely visszaadja a fájl tulajdonságait, például **fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)**.

* Hajtsa végre az eseményindítót egy műveletet, amely beolvassa a teljes fájlt, mint például a **fájl tartalmának beolvasása elérési út segítségével**, és rendelkezik a művelet használata [üzenet darabolás](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="examples"></a>Példák

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH aktiválása: Fájl hozzáadásakor és módosításakor

Ez az eseményindító indul el a logikai alkalmazás munkafolyamata egy fájl hozzáadásakor vagy módosítja a SFTP-kiszolgálóra. Ha például egy feltételt, amely ellenőrzi a fájl tartalmát, és a tartalmat, hogy a tartalom megfelel-e a megadott feltétel alapján is hozzáadhat. Ezután hozzáadhat egy műveletet, amely a fájl tartalmának beolvasása, és ezt a tartalmat egy mappába az SFTP-kiszolgáló használatával. 

**Példa vállalati**: Ez az eseményindító használatával figyelheti az SFTP-mappába, új fájlok, amelyek a vevői rendelések jelölésére. Ezután használhatja az SFTP-művelet például **fájl tartalmának beolvasása** úgy, hogy a rendelés tartalmának beolvasása a további feldolgozás céljából, és a egy rendelési adatbázisba sorrendben tárolja.

Fájl tartalmának kérésekor eseményindítók nem kap fájlok 50 MB-nál nagyobb. 50 MB-nál nagyobb fájlok lekéréséhez kövesse az ezt a mintát: 

* Használjon egy eseményindítót, amely visszaadja a fájl tulajdonságait, például **fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)**.

* Hajtsa végre az eseményindítót egy műveletet, amely beolvassa a teljes fájlt, mint például a **fájl tartalmának beolvasása elérési út segítségével**, és rendelkezik a művelet használata [üzenet darabolás](../logic-apps/logic-apps-handle-large-messages.md).

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - SSH művelet: Tartalom beolvasása elérési út alapján

Ez a művelet a tartalom olvas be egy fájl az SFTP-kiszolgálóra. Így például az előző példában és a egy feltételt, amely a fájl tartalmának meg kell felelnie az eseményindító is hozzáadhat. Ha a feltétel teljesül, a műveletet, amely lekérdezi a tartalmat futtathatja. 

Fájl tartalmának kérésekor eseményindítók nem kap fájlok 50 MB-nál nagyobb. 50 MB-nál nagyobb fájlok lekéréséhez kövesse az ezt a mintát: 

* Használjon egy eseményindítót, amely visszaadja a fájl tulajdonságait, például **fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)**.

* Hajtsa végre az eseményindítót egy műveletet, amely beolvassa a teljes fájlt, mint például a **fájl tartalmának beolvasása elérési út segítségével**, és rendelkezik a művelet használata [üzenet darabolás](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/sftpconnector/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
