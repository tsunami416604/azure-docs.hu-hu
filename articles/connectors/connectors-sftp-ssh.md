---
title: Csatlakozás SFTP-kiszolgálóhoz SSH-val
description: SSH és Azure Logic Apps alkalmazásokkal automatizálhatja az SFTP-kiszolgáló fájljait figyelő, létrehozó, kezelő, küldött és fogadó feladatokat
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 03/7/2020
tags: connectors
ms.openlocfilehash: d4ab7425c967d3a176c0a576d0be38ece1701b8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128413"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>SFTP-fájlok figyelése, létrehozása és kezelése SSH és Azure Logic Apps használatával

A [Biztonságos rendszerhéj (SSH)](https://www.ssh.com/ssh/protocol/) protokoll használatával a [Biztonságos fájlátviteli protokoll (SSH)](https://www.ssh.com/ssh/sftp/) protokollon lévő fájlok at figyelő, létrehozó, küldő és fogadó feladatok automatizálásához az Azure Logic Apps és az SFTP-SSH összekötő használatával integrációs munkafolyamatokat hozhat létre és automatizálhat. Az SFTP olyan hálózati protokoll, amely fájlhozzáférést, fájlátvitelt és fájlfelügyeletet biztosít valamilyen megbízható adatstreamen keresztül. Íme néhány példa, amelyet automatizálhat:

* A fájlok hozzáadásakor vagy módosításának figyelése.
* Fájlok beszerezni, létrehozása, másolása, átnevezése, frissítése, listázása és törlése.
* Mappák létrehozása.
* Fájltartalom és metaadatok beszerezése.
* Bontsa ki az archívumokat mappákba.

Olyan eseményindítókat használhat, amelyek figyelik az SFTP-kiszolgálóeseményeit, és a kimenetet más műveletek számára is elérhetővé teszik. Az SFTP-kiszolgálón különböző feladatokat végrehajtó műveleteket használhat. A logikai alkalmazásban más műveletek is rendelkezhetnek az SFTP-műveletek kimenetének használatával. Ha például rendszeresen lekéri a fájlokat az SFTP-kiszolgálóról, az Office 365 Outlook-összekötő vagy Outlook.com összekötő használatával e-mailben értesítéseket küldhet ezekről a fájlokról és azok tartalmáról. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Az SFTP-SSH-csatlakozó és az SFTP-csatlakozó közötti különbségekről a témakör későbbi részében olvassa el az [SFTP-SSH és az SFTP összehasonlítása](#comparison) című szakaszt.

## <a name="limits"></a>Korlátok

* A [darabolást](../logic-apps/logic-apps-handle-large-messages.md) támogató SFTP-SSH műveletek legfeljebb 1 GB-os fájlokat képesek kezelni, míg a darabolást nem támogató SFTP-SSH műveletek akár 50 MB-os fájlokat is képesek kezelni. Bár az alapértelmezett adattömb mérete 15 MB, ez a méret dinamikusan változhat, kezdve 5 MB és fokozatosan növekszik a maximum 50 MB-ra, olyan tényezők alapján, mint a hálózati késés, a kiszolgáló válaszideje és így tovább.

  > [!NOTE]
  > [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

  Ezt az adaptív viselkedést felülbírálhatja, ha [állandó adattömbméretet ad meg](#change-chunk-size) helyette. Ez a méret 5 MB és 50 MB között lehet. Tegyük fel például, hogy van egy 45 MB-os fájlja és egy hálózata, amely késleltetés nélkül támogatja ezt a fájlméretet. Az adaptív adattömbök több hívást eredményeznek, nem pedig egy hívást. A hívások számának csökkentése érdekében próbálja meg beadni az 50 MB-os adattömbméretet. Különböző esetekben, ha a logikai alkalmazás időtúllépés, például 15 MB-os adattömbök használata esetén, megpróbálhatja csökkenteni a méretet 5 MB-ra.

  Az adattömb mérete kapcsolathoz van társítva, ami azt jelenti, hogy ugyanazt a kapcsolatot használhatja az adattömböket támogató műveletekhez, majd olyan műveletekhez, amelyek nem támogatják az adattömböket. Ebben az esetben az adattömb mérete műveletek, amelyek nem támogatják a darabolás tartományok 5 MB és 50 MB között. Ez a táblázat azt mutatja, hogy mely SFTP-SSH műveletek támogatják a darabolást:

  | Műveletek | Darabolástámogatása | Adattömbméret-támogatás felülbírálása |
  |--------|------------------|-----------------------------|
  | **Fájl másolása** | Nem | Nem alkalmazható |
  | **Fájl létrehozása** | Igen | Igen |
  | **Mappa létrehozása** | Nem alkalmazható | Nem alkalmazható |
  | **Fájl törlése** | Nem alkalmazható | Nem alkalmazható |
  | **Archívum kibontása mappába** | Nem alkalmazható | Nem alkalmazható |
  | **Fájltartalom beszereznie** | Igen | Igen |
  | **Fájltartalom beszereznie az elérési út használatával** | Igen | Igen |
  | **Fájlmetaadatok beszerezése** | Nem alkalmazható | Nem alkalmazható |
  | **Fájlmetaadatok beszereznie elérési út használatával** | Nem alkalmazható | Nem alkalmazható |
  | **Fájlok listázása a mappában** | Nem alkalmazható | Nem alkalmazható |
  | **Fájl átnevezése** | Nem alkalmazható | Nem alkalmazható |
  | **Fájl frissítése** | Nem | Nem alkalmazható |
  ||||

* Az SFTP-SSH eseményindítók nem támogatják az üzenetdarabolást. Fájltartalom igénylésekor az eseményindítók csak a 15 MB-os vagy annál kisebb fájlokat jelölik ki. Ha 15 MB-nál nagyobb fájlokat szeretne beszerezni, kövesse az alábbi mintát:

  1. Használjon olyan SFTP-SSH eseményindítót, amely csak a fájl tulajdonságait adja vissza, például **amikor fájlt adnak hozzá vagy módosítanak (csak tulajdonságokat)**.

  1. Kövesse az eseményindítót az SFTP-SSH **Fájltartalom beolvasása** művelettel, amely beolvassa a teljes fájlt, és implicit módon használja az üzenetdarabolást.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH és SFTP összehasonlítása

Az alábbiakban az SFTP-SSH-összekötő és az SFTP-csatlakozó közötti további fontos különbségeket olvashatja:

* A [SSH.NET függvénytárat](https://github.com/sshnet/SSH.NET)használja , amely egy nyílt forráskódú Secure Shell (SSH) könyvtár, amely támogatja a .NET-et.

* Ez a **mappa létrehozása** műveletet tartalmazza, amely létrehoz egy mappát az SFTP-kiszolgáló megadott elérési útján.

* Az **Átnevezésfájl** műveletet tartalmazza, amely átnevez egy fájlt az SFTP-kiszolgálón.

* *Legfeljebb 1 órára*gyorsítótárazza az SFTP-kiszolgálóval létesített kapcsolatot, ami javítja a teljesítményt, és csökkenti a kiszolgálóhoz való csatlakozásra tett kísérletek számát. A gyorsítótárazási viselkedés időtartamának beállításához az SFTP-kiszolgáló SSH-konfigurációjában szerkesztheti a [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) tulajdonságot.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az SFTP-kiszolgáló címe és a fiók hitelesítő adatai, amelyek lehetővé teszik a logikai alkalmazás számára az SFTP-fiók elérését. Hozzá kell férnie egy SSH titkos kulcshoz és az SSH titkos kulcs jelszavához is. Nagy fájlok feltöltésekor a darabolás használatához olvasási és írási engedélyekre is szükség van az SFTP-kiszolgáló gyökérmappájához. Ellenkező esetben "401 jogosulatlan" hibaüzenet jelenik meg.

  > [!IMPORTANT]
  >
  > Az SFTP-SSH csatlakozó *csak* a titkos kulcsformátumokat, algoritmusokat és ujjlenyomatokat támogatja:
  >
  > * **Privát kulcsformátumok:** RSA (Rivest Shamir Adleman) és DSA (Digital Signature Algorithm) kulcsok openssh és ssh.com formátumban. Ha a személyes kulcs PuTTY (.ppk) fájlformátumban van, először [konvertálja a kulcsot OpenSSH (.pem) fájlformátumra](#convert-to-openssh).
  >
  > * **Titkosítási algoritmusok:** DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC és AES-256-CBC
  >
  > * **Ujjlenyomat**: MD5
  >
  > Miután hozzáadja a kívánt SFTP-SSH eseményindítót vagy műveletet a logikai alkalmazáshoz, meg kell adnia az SFTP-kiszolgáló kapcsolati adatait. Ha az SSH személyes kulcsát adja meg ehhez a kapcsolathoz, ***ne írja be vagy ne írja be manuálisan a kulcsot,*** ami a kapcsolat megszakadását okozhatja. Ehelyett győződjön meg arról, hogy ***másolja a kulcsot az*** SSH titkos kulcsfájlból, és ***illessze*** be a kulcsot a kapcsolat részleteibe. 
  > További információt a cikk későbbi részében az [SFTP-hez való csatlakozás SSH-val](#connect) című szakaszban talál.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez hozzá szeretne férni az SFTP-fiókhoz. Ha egy SFTP-SSH eseményindítóval szeretne [kezdeni, hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) SFTP-SSH művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="how-sftp-ssh-triggers-work"></a>Az SFTP-SSH indítók működése

Az SFTP-SSH az SFTP fájlrendszer lekérdezésével és a legutóbbi szavazás óta megváltozott fájlok keresésével indítja el a munkát. Egyes eszközök lehetővé teszik az időbélyeg megőrzését a fájlok módosításakor. Ezekben az esetekben le kell tiltania ezt a funkciót, hogy az eseményindító működhessen. Íme néhány gyakori beállítás:

| SFTP-ügyfél | Műveletek |
|-------------|--------|
| Megnyerő | Ugrás a **Beállítások** > **beállításai hoz** > **Átvitel** > **szerkesztése** > **időbélyeg-megőrzésletiltása** > **Disable** |
| Filezilla | Ugrás az **Átvitt** > **fájlok időbélyegeinek megőrzése** > **című** tovább |
|||

Amikor egy eseményindító új fájlt talál, az eseményindító ellenőrzi, hogy az új fájl teljes, és nem részben írt. Előfordulhat például, hogy egy fájl folyamatban van, amikor az eseményindító ellenőrzi a fájlkiszolgálót. A részlegesen írott fájl visszaadásának elkerülése érdekében az eseményindító megjegyzi a legutóbbi módosításokat tartalmazó fájl időbélyegét, de nem adja vissza azonnal a fájlt. Az eseményindító csak a kiszolgáló ismételt lekérdezésekénekután adja vissza a fájlt. Néha ez a viselkedés az eseményindító lekérdezési időközének akár kétszeresét is okozhatja késleltetést okozhat.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>PuTTY-alapú kulcs konvertálása OpenSSH-ra

Ha a személyes kulcs PuTTY formátumban van, amely a .ppk (PuTTY Private Key) fájlnévkiterjesztést használja, először konvertálja a kulcsot OpenSSH formátumra, amely a .pem (Privacy Enhanced Mail) fájlnévkiterjesztést használja.

### <a name="unix-based-os"></a>Unix-alapú operációs rendszer

1. Ha a PuTTY eszközök még nincsenek telepítve a rendszerre, tegye meg most, például:

   `sudo apt-get install -y putty`

1. Futtassa ezt a parancsot, amely létrehoz egy fájlt, amely használható az SFTP-SSH összekötővel:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Példa:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows operációs rendszer

1. Ha még nem tette meg, [töltse le a legújabb PuTTY Generator (puttygen.exe) eszközt](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), majd indítsa el az eszközt.

1. Ezen a képernyőn válassza a **Betöltés**lehetőséget.

   ![Válassza a "Load" lehetőséget](./media/connectors-sftp-ssh/puttygen-load.png)

1. Tallózással keresse meg a személyes kulcsfájlt PuTTY formátumban, és válassza a **Megnyitás**gombot.

1. A **Konverziók** menüben válassza **az OpenSSH billentyű exportálása lehetőséget.**

   ![Válassza az "OpenSSH kulcs exportálása" lehetőséget](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Mentse a személyes kulcsfájlt a `.pem` fájlnévkiterjesztéssel.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Csatlakozás SFTP-hez SSH-val

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Üres logikai alkalmazások esetén a `sftp ssh` keresőmezőbe írja be szűrőként. Az eseményindítók listában válassza ki a kívánt eseményindítót.

   – vagy –

   Meglévő logikai alkalmazások esetén az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** A keresőmezőbe írja `sftp ssh` be szűrőként. A műveletek listájában jelölje ki a kívánt műveletet.

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

1. Adja meg a kapcsolathoz szükséges részleteket.

   > [!IMPORTANT]
   >
   > Amikor megadja az SSH személyes kulcsát az **SSH személyes** kulcstulajdonságában, kövesse az alábbi további lépéseket, amelyek segítenek abban, hogy megadja a tulajdonság teljes és helyes értékét. Egy érvénytelen kulcs a kapcsolat megszakadását okozza.

   Bár bármilyen szövegszerkesztőt használhat, az alábbi mintalépések bemutatják, hogyan másolhatja és illesztheti be helyesen a kulcsot a Notepad.exe használatával példaként.

   1. Nyissa meg az SSH titkoskulcsfájlt egy szövegszerkesztőben. Ezek a lépések példaként a Jegyzettömböt használják.

   1. A Jegyzettömb **Szerkesztés menüjében** válassza **az Összes kijelölése parancsot.**

   1. Válassza a > **Másolat** **szerkesztése**lehetőséget.

   1. A hozzáadott SFTP-SSH eseményindítóba vagy műveletbe illessze be a *teljes* kulcsot, amelyet másolt az **SSH titkoskulcs-tulajdonságába,** amely több sort is támogat.  ***Győződjön meg róla, hogy beilleszti*** a kulcsot. ***Ne írja be manuálisan a kulcsot, és ne is szerkeszthesse azt.***

1. Ha végzett a kapcsolat részleteinek megadásával, válassza a **Létrehozás gombot.**

1. Most adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Adattömb méretének felülbírálása

Az adattömbök által használt alapértelmezett adaptív viselkedés felülbírálásához 5 MB és 50 MB közötti állandó adattömbméretet adhat meg.

1. A művelet jobb felső sarkában jelölje ki a három pont gombot (**...**), majd a **Beállítások lehetőséget.**

   ![SFTP-SSH-beállítások megnyitása](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. A **Tartalomátvitel**csoportban az **Adattömbméret** tulajdonságban `5` adjon `50`meg egy egész értéket a értékből a értékbe, például: 

   ![Adja meg a helyette használandó adattömbméretet](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Ha elkészült, válassza a **Kész** lehetőséget.

## <a name="examples"></a>Példák

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH trigger: Fájl hozzáadásakor vagy módosításakor

Ez az eseményindító logikai alkalmazás munkafolyamatát indítja el, amikor egy SFTP-kiszolgálón hozzáadnak vagy módosítanak egy fájlt. Hozzáadhat például egy feltételt, amely ellenőrzi a fájl tartalmát, és lekéri a tartalmat annak alapján, hogy a tartalom megfelel-e egy adott feltételnek. Ezután hozzáadhat egy műveletet, amely beszerzi a fájl tartalmát, és a tartalmat az SFTP-kiszolgáló egyik mappájába helyezi.

**Példa vállalati például**: Ezzel az eseményindítóval figyelheti az Ügyfélrendeléseket képviselő új fájlok SFTP mappáját. Ezután használhat egy SFTP-műveletet, például a **Fájltartalom begetése,** így a rendelés tartalmát további feldolgozásra lekell kérni, és a rendeléseket egy rendelési adatbázisban tárolhatja.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - SSH művelet: Tartalom beszerezni elérési útját

Ez a művelet leveszi a tartalmat egy SFTP-kiszolgálón lévő fájlból. Így például hozzáadhatja az előző példából származó eseményindítót, és egy olyan feltételt, amelynek a fájl tartalmának meg kell felelnie. Ha a feltétel igaz, a művelet, amely megkapja a tartalmat futtathatja.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az eseményindítókat, műveleteket és korlátokat az összekötő Swagger-fájlja szerint lásd az [összekötő referencialapján.](https://docs.microsoft.com/connectors/sftpwithssh/)

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
