---
title: Kapcsolódás az SFTP-kiszolgálóhoz SSH-Azure Logic Apps
description: Automatizálhatja az SFTP-kiszolgálókhoz tartozó fájlok figyelését, létrehozását, kezelését, küldését és fogadását SSH és Azure Logic Apps használatával.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 33c6007ebc429bb0d95d702ae9b90f9ac411a88c
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695189"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>SFTP-fájlok figyelése, létrehozása és kezelése SSH és Azure Logic Apps használatával

A [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokoll használatával a biztonságos [File Transfer Protocol-(SFTP-)](https://www.ssh.com/ssh/sftp/) kiszolgálón lévő fájlok figyelésére, létrehozására, küldésére és fogadására szolgáló feladatok automatizálásához az integrációs munkafolyamatokat Azure Logic apps és az SFTP-SSH használatával is felépítheti és automatizálhatja. összekötő. Az SFTP egy hálózati protokoll, amely a fájlok elérését, a fájlátvitel és a fájlok felügyeletét teszi lehetővé bármilyen megbízható adatfolyamon. Íme néhány példa a feladatok automatizálására:

* A fájlok hozzáadásának vagy módosításának figyelése.
* Fájlok lekérése, létrehozása, másolása, átnevezése, frissítése, listázása és törlése.
* Mappák létrehozása.
* Fájl tartalmának és metaadatainak beolvasása.
* Archívumok kinyerése mappákba.

Az SFTP-kiszolgálón lévő eseményeket figyelő eseményindítókat használhat, és a kimenetet más műveletek számára is elérhetővé teheti. Az SFTP-kiszolgálón különféle feladatokat végző műveleteket is használhat. A logikai alkalmazásban más műveletek is használhatók az SFTP-műveletek kimenetének használatával. Ha például az SFTP-kiszolgálóról rendszeresen lekéri a fájlokat, e-mail-riasztásokat küldhet a fájlokról és azok tartalmáról az Office 365 Outlook Connector vagy a Outlook.com Connector használatával. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

Az SFTP-SSH-összekötő és az SFTP-összekötő közötti különbségekért tekintse át a témakör későbbi, az [SFTP-SSH és az SFTP összehasonlítása](#comparison) című szakaszát.

## <a name="limits"></a>Korlátok

* Az SFTP-SSH-műveletek alapértelmezés szerint *1 GB vagy kisebb* fájlok olvasását vagy írását írják elő, de egyszerre csak *15 MB* -onként. A 15 MB-nál nagyobb fájlok kezeléséhez az SFTP-SSH műveletek támogatják az [üzenetek darabolását](../logic-apps/logic-apps-handle-large-messages.md), kivéve a fájl másolása műveletet, amely csak 15 MB-os fájlt képes kezelni. A **fájl tartalmának beolvasása** művelet implicit módon használja az üzenetek darabolását.

* SFTP – az SSH-eseményindítók nem támogatják a darabolást. Fájl tartalmának kérésekor az eseményindítók csak a 15 MB vagy annál kisebb fájlokat jelölik ki. A 15 MB-nál nagyobb fájlok lekéréséhez kövesse az alábbi mintát:

  * Használjon olyan SFTP-SSH-triggert, amely a fájl tulajdonságait adja vissza, például **egy fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)** .

  * Kövesse a triggert az SFTP-SSH- **Get fájl tartalma** művelettel, amely beolvassa a teljes fájlt, és implicit módon használja az üzenetek darabolását.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Az SFTP-SSH és az SFTP összehasonlítása

Íme az SFTP-SSH-összekötő és az SFTP-összekötő, ahol az SFTP-SSH összekötő az alábbi képességekkel rendelkezik:

* A a [SSH.net könyvtárat](https://github.com/sshnet/SSH.NET)használja, amely a .NET-et támogató nyílt forráskódú Secure Shell-(SSH-) kódtár.

* Az SFTP-SSH-műveletek alapértelmezés szerint *1 GB vagy kisebb* fájlok olvasását vagy írását írják elő, de egyszerre csak *15 MB* -onként. A 15 MB-nál nagyobb fájlok kezeléséhez az SFTP-SSH műveletek az [üzenetek darabolását](../logic-apps/logic-apps-handle-large-messages.md)is használhatják. A fájl másolása művelet azonban csak 15 MB-nyi fájlt támogat, mivel ez a művelet nem támogatja az üzenetek darabolását. SFTP – az SSH-eseményindítók nem támogatják a darabolást.

* Megadja a **mappa létrehozása** műveletet, amely létrehoz egy MAPPÁT az SFTP-kiszolgálón megadott elérési úton.

* Megadja a **fájl átnevezése** műveletet, amely átnevezi az SFTP-kiszolgálón található fájlt.

* *Akár 1 óráig*is gyorsítótárazza a kapcsolatot az SFTP-kiszolgálóval, ami javítja a teljesítményt, és csökkenti a próbálkozások számát a kiszolgálóhoz való csatlakozáskor. A gyorsítótárazási viselkedés időtartamának beállításához szerkessze a [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) tulajdonságot az SFTP-kiszolgálón található SSH-konfigurációban.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az SFTP-kiszolgáló címe és a fiók hitelesítő adatai, amelyek lehetővé teszik a logikai alkalmazás számára az SFTP-fiók elérését. Emellett hozzá kell férnie egy SSH titkos kulcshoz és az SSH titkos kulcs jelszavához.

  > [!IMPORTANT]
  >
  > Az SFTP-SSH összekötő *csak* ezeket a titkos kulcs formátumait, algoritmusait és ujjlenyomatait támogatja:
  >
  > * **Titkos kulcsok formátuma**: Az RSA (Rivest Adleman) és a DSA (digitális aláírási algoritmus) kulcsai mind az OpenSSH-, mind a ssh.com-formátumokban. Ha a titkos kulcs Putty (. PPK) fájlformátumban van, először [alakítsa át a kulcsot az OpenSSH (. PEM)](#convert-to-openssh)fájlformátumba.
  >
  > * **Titkosítási algoritmusok**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC és AES-256-CBC
  >
  > * **Ujjlenyomat**: MD5
  >
  > Miután hozzáadta az SFTP-SSH-triggert vagy a logikai alkalmazáshoz használni kívánt műveletet, meg kell adnia az SFTP-kiszolgáló kapcsolódási adatait. Ha megadja az SSH titkos kulcsát ehhez a csatlakozáshoz, ***ne adja meg manuálisan a kulcsot, vagy szerkessze a kulcsot***, ami miatt a kapcsolódás sikertelen lehet. Ehelyett a ***kulcsot másolja*** a titkos SSH-kulcs fájljából, és ***illessze*** be a kulcsot a kapcsolat részleteibe. 
  > További információkért lásd a jelen cikk a [Kapcsolódás az SFTP-hez SSH-val](#connect) című szakaszát.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, ahová el szeretné érni az SFTP-fiókját. Egy SFTP-SSH triggerrel való kezdéshez [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha SFTP-SSH műveletet szeretne használni, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="how-sftp-ssh-triggers-work"></a>Az SFTP-SSH-triggerek működése

Az SFTP-SSH elindítja az SFTP fájlrendszer lekérdezésével és a legutóbbi lekérdezés óta módosult fájlok keresésével kapcsolatos munkát. Egyes eszközök lehetővé teszik, hogy a fájlok változásakor őrizze meg az időbélyeget. Ezekben az esetekben le kell tiltania ezt a funkciót, így az trigger működhet. Íme néhány gyakori beállítás:

| SFTP-ügyfél | Action |
|-------------|--------|
| WinSCP | Ugrás a **Beállítások** > **Beállítások**átvitel szerkesztési megőrzési időbélyegénekletiltása >  >  >  >  |
| Filezillát | Ugrás az > **átvitt fájlok** > adatmegőrzési időbélyegére –**Letiltás** |
|||

Ha egy trigger új fájlt talál, az trigger ellenőrzi, hogy az új fájl elkészült-e, és nem részlegesen van-e írva. Előfordulhat például, hogy egy fájl változása folyamatban van, amikor az trigger ellenőrzi a fájlkiszolgálón. Egy részlegesen megírt fájl visszaadásának elkerüléséhez az trigger megállapítja a legutóbbi módosításokat tartalmazó fájl időbélyegét, de nem adja vissza azonnal a fájlt. Az trigger csak akkor adja vissza a fájlt, ha újra kérdezi le a kiszolgálót. Előfordulhat, hogy ez a viselkedés egy késleltetést okoz, amely akár kétszer is meghaladhatja az aktiválás lekérdezési időközét.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Putty-alapú kulcs konvertálása az OpenSSH-ra

Ha a titkos kulcs Putty formátumú, amely a. PPK (Putty titkos kulcs) fájlnévkiterjesztést használja, először alakítsa át a kulcsot az OpenSSH formátumba, amely a. PEM (Privacy Enhanced Mail) fájlnévkiterjesztést használja.

### <a name="unix-based-os"></a>UNIX-alapú operációs rendszer

1. Ha a PuTTY-eszközök még nincsenek telepítve a rendszeren, tegye meg most, például:

   `sudo apt-get install -y putty`

1. Futtassa ezt a parancsot, amely létrehoz egy fájlt, amely az SFTP-SSH összekötővel használható:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Példa:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows operációs rendszer

1. Ha még nem tette meg, [töltse le a legújabb Putty Generator (PuTTYgen. exe) eszközt](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), majd indítsa el az eszközt.

1. Ezen a képernyőn válassza a **Betöltés**lehetőséget.

   ![Válassza a "betöltés" lehetőséget](./media/connectors-sftp-ssh/puttygen-load.png)

1. Tallózással keresse meg a titkos kulcs fájlját Putty formátumban, majd válassza a **Megnyitás**lehetőséget.

1. A **konverziók** menüben válassza az OpenSSH- **kulcs exportálása**lehetőséget.

   ![Válassza az "OpenSSH-kulcs exportálása" lehetőséget](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Mentse a titkos kulcs fájlját a `.pem` fájlnévkiterjesztés megnyomásával.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Kapcsolódás az SFTP-hez SSH-val

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be a "SFTP SSH" kifejezést a szűrőként. Válassza ki a kívánt eseményindítót az eseményindítók listából.

   – vagy –

   Meglévő Logic apps esetén az utolsó lépésben, amelyhez műveletet szeretne hozzáadni, válassza az **új lépés**lehetőséget. A keresőmezőbe írja be a "SFTP SSH" kifejezést a szűrőként. A műveletek listában válassza ki a kívánt műveletet.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. Adja meg a kapcsolathoz szükséges adatokat.

   > [!IMPORTANT]
   >
   > Ha megadja az SSH titkos kulcsát az **SSH titkos kulcs** tulajdonságában, kövesse ezeket a további lépéseket, amelyekkel biztosíthatja, hogy a tulajdonság teljes és megfelelő értékét adja meg. Érvénytelen kulcs miatt a kapcsolódás sikertelen lesz.

   Habár használhat bármely szövegszerkesztőt, itt láthatók azok a lépések, amelyek bemutatják, hogyan lehet helyesen másolni és beilleszteni a kulcsot a Notepad. exe használatával példaként.

   1. Nyissa meg az SSH titkos kulcs fájlját egy szövegszerkesztőben. Ezek a lépések példaként használják a jegyzettömböt.

   1. A Jegyzettömb **Szerkesztés** menüjében válassza az **összes kijelölése**lehetőséget.

   1. Válassza a **szerkesztés** > **másolat**elemet.

   1. Az SFTP-SSH-trigger vagy a hozzáadott művelet esetében illessze be a *teljes* kulcsot, amelyet a **titkos SSH-kulcs** tulajdonságba másolt, amely több sort is támogat.  ***Ügyeljen rá, hogy illessze be*** a kulcsot. ***Ne adja meg manuálisan a kulcsot, vagy szerkessze***azt.

1. Ha végzett a kapcsolat részleteinek megadásával, válassza a **Létrehozás**lehetőséget.

1. Most adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="examples"></a>Példák

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP – SSH-trigger: Fájl hozzáadásakor vagy módosításakor

Ez az aktiválás egy logikai alkalmazás munkafolyamatát indítja el, amikor egy fájlt hozzáadnak vagy módosítanak egy SFTP-kiszolgálón. Hozzáadhat például egy olyan feltételt, amely ellenőrzi a fájl tartalmát, és beolvassa a tartalmat attól függően, hogy a tartalom megfelel-e a megadott feltételnek. Ezután hozzáadhat egy műveletet, amely beolvassa a fájl tartalmát, és az SFTP-kiszolgáló egy mappájába helyezi a tartalmat.

**Vállalati példa**: Ezzel a triggerrel figyelheti az SFTP-mappát az ügyfelek rendeléseit jelölő új fájlok esetében. Ezután használhat egy SFTP-műveletet, például a **fájlok beolvasása** lehetőséget, így a sorrend tartalmát megtekintve további feldolgozást hajthat végre, és a rendelést egy Orders adatbázisban tárolhatja.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP-SSH művelet: Tartalom beolvasása elérési út használatával

Ez a művelet lekérdezi a tartalmat egy SFTP-kiszolgálón lévő fájlból. Így például felveheti az triggert az előző példából, és egy olyan feltételt, amelynek meg kell felelnie a fájl tartalmának. Ha a feltétel igaz, akkor a tartalmat lekérdező művelet futtatható.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/sftpconnector/).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
