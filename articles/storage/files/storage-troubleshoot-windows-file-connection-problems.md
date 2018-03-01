---
title: "A Windows Azure fájlok problémák megoldása |} Microsoft Docs"
description: "Windows Azure fájlok problémák hibaelhárítása"
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: genli
ms.openlocfilehash: 073d163e139c9fd400e4b3177c26d4ddb6228ed0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>A Windows Azure fájlok problémák megoldásához

Ez a cikk a Microsoft Azure fájlok Windows-ügyfelek csatlakozásakor kapcsolatos gyakori problémák sorolja fel. Is biztosít lehetséges okokért és megoldásokért ezeket a problémákat. Ebben a cikkben a hibaelhárítási mellett is használhatja [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) győződjön meg arról, hogy a Windows ügyfél-környezet van-e a megfelelő előfeltételek. AzFileDiagnostics automatizálja a jelenség a cikkben említett, és a segítségével állítsa be a környezetet az optimális teljesítmény eléréséhez a legtöbb észlelése. Ezt az információt is tájékozódhat a [Azure fájlmegosztási hibaelhárító](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) , amelyek segítenek a kapcsolódás/leképezési/csatlakoztatása Azure fájlmegosztási problémák lépéseit.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>53-as hiba, a 67-es hiba vagy a hiba 87, amikor csatlakoztatni vagy leválasztani az Azure fájlmegosztások

Amikor fájlmegosztást csatlakoztatni a helyszíni vagy egy másik datacenter próbál, akkor fordulhat elő, a következő hibákat:

- Rendszer 53-as hiba. A hálózati elérési út nem található.
- 67-es rendszerhiba történt. A hálózatnév nem található.
- 87 rendszerhiba történt. A paraméter nem megfelelő.

### <a name="cause-1-unencrypted-communication-channel"></a>1. ok: Titkosítatlan kommunikációs csatorna

Biztonsági okokból Azure fájlmegosztásokat kapcsolatok sem, ha a kommunikációs csatorna nincs titkosítva, és ha a kapcsolódás nem ugyanabban az adatközpontban a Azure fájlmegosztásokat tároló. Csak akkor, ha a felhasználó ügyfél operációs rendszer támogatja az SMB-titkosítás a kommunikációs csatorna titkosítást biztosítja.

Windows 8, Windows Server 2012 és egyes rendszerek újabb verzióit egyezteti az SMB 3.0-s, amely támogatja a titkosítást kérelmeket.

### <a name="solution-for-cause-1"></a>Megoldás ok 1

Csatlakozás egy ügyfél, amely az alábbi:

- Megfelel a Windows 8 és Windows Server 2012 vagy újabb verzió
- A virtuális gép ugyanabban az adatközpontban, az Azure storage-fiók Azure fájlmegosztás használt csatlakozik

### <a name="cause-2-port-445-is-blocked"></a>2. ok: 445-ös Port blokkolva van

Rendszerhiba 53-as vagy rendszerhiba 67 akkor fordulhat elő, ha egy fájl Azure adatközpontba kimenő kommunikáció 445-ös port blokkolva van. Szolgáltatók, amely a 445-ös porton elérésének engedélyezése vagy letiltása az összegzés megtekintéséhez keresse fel [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Ha szeretné megtudni, hogy ez-e a "System error 53" üzenet mögött OK, Portqry segítségével lekérdezni a TCP:445 végpont. A TCP:445 végpont szűrt jelenik meg, ha blokkolva van, az TCP-portot. Íme egy példa lekérdezést:

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Ha egy szabály a hálózati elérési út mentén blokkolja a 445-ös TCP-portot, a következő eredmény jelenik meg:

  `TCP port 445 (microsoft-ds service): FILTERED`

Portqry használatával kapcsolatos további információkért lásd: [a Portqry.exe parancssori segédprogram](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Megoldás ok 2

Az informatikai részleg nyissa meg a kimenő 445-ös porton való együttműködésre [Azure IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>3. ok: NTLMv1 engedélyezve

Rendszerhiba 53-as vagy rendszerhiba 87 akkor fordulhat elő, ha NTLMv1 kommunikáció engedélyezve van az ügyfélen. Az Azure Files csak NTLMv2-alapú hitelesítést támogatja. Egy kevésbé biztonságos ügyfél engedélyezett NTLMv1 rendelkező hoz létre. Ezért kommunikációja blokkolva van az Azure Fileshoz. 

Annak megállapításához, hogy ez-e a hiba okának, ellenőrizze, hogy az alábbi beállításjegyzékbeli alkulcs 3 értéket:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

További információkért lásd: a [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) a témakör a TechNet webhelyen.

### <a name="solution-for-cause-3"></a>Megoldás ok 3

Állítsa vissza a **LmCompatibilityLevel** az alapértelmezett érték a következő beállításkulcsban 3 értéket:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Hiba 1816 "nincs elegendő kvótája: Ez a parancs" Azure-fájlmegosztásra másolásakor

### <a name="cause"></a>Ok

1816 hiba akkor fordul elő, amikor elérte a felső korlátja egyidejű megnyitott leíróinak egy fájlt a számítógépen, amelyen a fájlmegosztás csatlakoztatva számára engedélyezett.

### <a name="solution"></a>Megoldás

Adjon meg kevesebb egyidejű megnyitott leíróinak néhány leírók bezárásával, majd próbálkozzon újra. További információkért lásd: [Microsoft Azure Storage teljesítményére és méretezhetőségére ellenőrzőlista](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Lassú fájl másolása és a Windows Azure-fájlokból

Lassú teljesítmény fájlok átvitele az Azure File service megkísérlésekor jelenhet meg.

- Ha nem rendelkezik egy adott minimális i/o vonatkozó méretbeli követelményt, ajánlott, hogy 1 MB, az I/O méretéről az optimális teljesítmény érdekében.
-   Ha ismeri a végleges mérethez, amelyet az írási műveletek kiterjesztése, és a szoftver nincs kompatibilitási problémákat, ha a fájl unwritten utóhívás nullákat tartalmaz, majd állítsa be a fájlméret, így minden egyes egy kibővítése írási helyett előre.
-   A jobb oldali copy metódust használja:
    -   Használjon [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) bármely két fájlmegosztások közötti átvitel céljából.
    -   Használjon [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) közötti fájlmegosztások a helyi számítógépen.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 vagy Windows Server 2012 R2 szempontjai

Windows 8.1 vagy Windows Server 2012 R2 rendszert futtató ügyfeleken győződjön meg arról, hogy a [KB3114025](https://support.microsoft.com/help/3114025) gyorsjavítás telepítve van. Ez a gyorsjavítás javítja a teljesítményt a Create, és zárja be a kezeli.

Ellenőrizze, hogy a gyorsjavítás telepítve van-e a következő parancsfájl futtatásával is:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Ha a gyorsjavítás telepítve van, a következő eredmény jelenik meg:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Windows Server 2012 R2 Azure piactéren képeknek gyorsjavítás KB3114025 alapértelmezés szerint telepítve 2015. decemberi kezdve.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Ne legyen a meghajtóbetűjel mappa **Sajátgép**

Ha az az Azure fájlmegosztások rendszergazdaként a nettó használja, a megosztás úgy tűnik, hogy hiányzik.

### <a name="cause"></a>Ok

Alapértelmezés szerint a Windows Fájlkezelőben nem futtassa rendszergazdaként. Ha nettó használata futtatja a parancsot egy rendszergazdai parancssort, akkor a hálózati meghajtó meghajtó csatlakoztatása rendszergazdaként. Mivel a csatlakoztatott meghajtók felhasználó-központú, van-e bejelentkezett felhasználói fiók nem jelenik meg a meghajtók ha csatlakoztatva vannak egy másik felhasználói fiókkal.

### <a name="solution"></a>Megoldás
A megosztás csatlakoztatásához a nem rendszergazdai parancssorból. Azt is megteheti, amelyeket követve [a TechNet témakör](https://technet.microsoft.com/library/ee844140.aspx) konfigurálása a **EnableLinkedConnections** beállításazonosítót.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Net use parancs sikertelen lesz, ha a tárfiók perjelet tartalmaz

### <a name="cause"></a>Ok

A net use parancs perjellel (/) értelmezi a parancssori kapcsolót. Ha a felhasználói fiók nevét perjellel kezdődik, a meghajtó hozzárendelése sikertelen lesz.

### <a name="solution"></a>Megoldás

A probléma megoldásához használhatja az alábbi lépések egyikét:

- Futtassa az alábbi PowerShell-parancsot:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  Egy parancsfájlból futtathatja a parancs így:

  `Echo new-smbMapping ... | powershell -command –`

- Helyezze a kulcsot a probléma megoldásához--dupla idézőjelek közé, kivéve, ha a perjel karaktert. Ha igen, használja az interaktív módban, és külön-külön írja be a jelszót, vagy újragenerálja a kulcsokat nem perjellel kezdődik a kulcs beszerzése.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Alkalmazás vagy szolgáltatás nem tud hozzáférni az Azure Fileshoz csatlakoztatott meghajtó

### <a name="cause"></a>Ok

Felhasználónként csatlakoztatott meghajtók. Ha az alkalmazás vagy szolgáltatás, hogy a meghajtó csatlakoztatott egy másik felhasználói fiók alatt fut, az alkalmazás nem jelenik meg a meghajtót.

### <a name="solution"></a>Megoldás

Használja az alábbi megoldások valamelyikét:

-   Ugyanazzal a fiókkal, amely tartalmazza az alkalmazás csatlakoztathatja a meghajtót. Használhatja például a PsExec eszköz.
- A tárfiók nevét és a kulcs továbbítja a felhasználó nevében, és a password paraméter a háló paranccsal.
- A cmdkey paranccsal adja hozzá a hitelesítő adatokat a hitelesítőadat-kezelő. A szolgáltatás fiók környezetében, az interaktív bejelentkezési vagy runas használatával parancssorból ezt elvégezni.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Képezze le a megosztást közvetlenül csatlakoztatott meghajtóbetűjel használata nélkül. Egyes alkalmazások esetleg nem csatlakozik újra, arra a meghajtóra megfelelően, megbízhatóbb a teljes UNC elérési úton keresztül lehet. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Kövesse ezeket az utasításokat, miután nettó használja a rendszer és a hálózati szolgáltatás fiók futtatásakor fordulhat elő a következő hibaüzenet: "1312 rendszerhiba történt. A megadott bejelentkezési munkamenet nem létezik. Azt is, hogy már befejeződött." Ha ez történik, ügyeljen arra, hogy a felhasználónév nettó használata átadott tartományadatokat (például: "[tárfiók neve]. file.core.windows .net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>"Másolja át a fájlt, amely nem támogatja a titkosítást egy célra" hiba

A hálózaton keresztül másolja a fájlt, ha a fájl visszafejtése a forrásszámítógépen, nem titkosított szövegként továbbított, és újból titkosítja a célhelyen. Azonban megjelenhet a következő hiba történt egy titkosított fájl másolása közben: "A rendszer másolja a fájlt egy cél, amely nem támogatja a titkosítást."

### <a name="cause"></a>Ok
Ez a probléma akkor fordulhat elő, ha titkosított fájlrendszer (EFS) használja. A BitLocker által titkosított fájlok átmásolhatók az Azure Fileshoz. Azure-fájlok azonban nem támogatja az NTFS EFS.

### <a name="workaround"></a>Megkerülő megoldás
Fájl másolása a hálózaton keresztül, akkor először vissza kell fejtenie azt. Az alábbi módszerek valamelyikével:

- Használja a **/d másolása** parancsot. Lehetővé teszi a titkosított fájlokat, mint a célhely visszafejtet fájlokat menteni.
- Állítsa be a következő beállításkulcsot:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Értéktípus = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Érték = 1

Vegye figyelembe, hogy a beállításkulcs megadása hatással van minden, hálózati megosztások végrehajtott másolási műveleteket.

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a gyorsan megoldott probléma eléréséhez.
