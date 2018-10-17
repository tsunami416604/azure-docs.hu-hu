---
title: A Windows Azure Files-problémák elhárítása |} A Microsoft Docs
description: A Windows Azure Files-problémák hibaelhárítása
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: a0a330d3ea7362ffabb20a5d390cee87cbf7d8ff
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365405"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>A Windows Azure Files-problémák hibaelhárítása

Ez a cikk a gyakori problémák a Microsoft Azure Files kapcsolódó Windows-ügyfelek csatlakozáskor sorolja fel. Is biztosít a lehetséges okokért és megoldásokért ezeket a problémákat. A hibaelhárítási lépéseket ebben a cikkben mellett is használhatja [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) , hogy a Windows ügyfél környezet legyen-e a megfelelő előfeltételek. AzFileDiagnostics automatizálja a jelenség a jelen cikkben említett, és a segítségével állítsa be a környezetet az optimális teljesítmény eléréséhez a legtöbb felismerése. Ezt az információt is megtalálhatja a [Azure-fájlmegosztási hibaelhárító](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) , amelyek segítenek a problémák az Azure Files csatlakozás és leképezés/csatlakoztatási fájlmegosztások lépéseit ismerteti.


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>53-as hiba, a 67-es hiba vagy a hiba 87 csatlakoztatása vagy leválasztása az Azure-fájlmegosztások

Amikor egy fájlmegosztás csatlakoztatása a helyszíni, illetve egy másik adatközpontban oldja meg, a következő hibákat kaphat:

- 53-as rendszerhiba történt. A hálózati elérési út nem található.
- 67-es rendszerhiba történt. A hálózatnév nem található.
- 87 rendszerhiba történt. A paraméter helytelen.

### <a name="cause-1-unencrypted-communication-channel"></a>1. ok: Nem titkosított kommunikációs csatornát

Biztonsági okokból az Azure-fájlmegosztások kapcsolatok le lesznek tiltva, ha a kommunikációs csatornát nincs titkosítva, és ha a csatlakozási kísérlet nem ugyanabban az adatközpontban az Azure-fájlmegosztások-ket. Titkosítatlan kapcsolat az adatközpontokon belül is blokkolhatók, ha a [biztonságos átvitelre van szükség](https://docs.microsoft.com/en-us/azure/storage/common/storage-require-secure-transfer) beállítás engedélyezve van a tárfiókon. Kommunikációs csatorna titkosítási van megadva, csak akkor, ha a felhasználó ügyfél operációs rendszere támogatja az SMB-titkosítás.

A Windows 8, Windows Server 2012 és egyes rendszerek újabb verzióit egyeztetni az SMB 3.0-s, amely támogatja a titkosítást tartalmazó kérelmeket.

### <a name="solution-for-cause-1"></a>1 OK megoldás

1. Ellenőrizze a [biztonságos átvitelre van szükség](https://docs.microsoft.com/en-us/azure/storage/common/storage-require-secure-transfer) beállítás le van tiltva, a tárfiókban.
2. Csatlakozás ügyfélről, amely az alábbi lehetőségek közül:

    - Megfelel a követelményeknek, Windows 8 és Windows Server 2012 vagy újabb verzió
    - Csatlakoztatja a virtuális gépről ugyanabban az adatközpontban, az Azure-fájlmegosztás használt Azure storage-fiókban

### <a name="cause-2-port-445-is-blocked"></a>2. ok: 445-ös Port le van tiltva

Rendszerhiba: 53-as vagy 67-es rendszerhiba akkor fordulhat elő, ha az Azure Files-adatközpont a kimenő kommunikáció 445-ös port le van tiltva. Tekintse meg, hogy a 445-ös port elérésének engedélyezése vagy letiltása az internetszolgáltatók összegzését, lépjen a [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Szeretné megtudni, hogy a "System error 53-as" üzenet hibakezelése áll, a Portqry segítségével lekérdezheti a TCP:445 végpont. Ha a TCP:445 végpont szűrtként jelenik meg, a TCP-port le van tiltva. Itt láthat egy példalekérdezést:

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Ha a 445-ös TCP-portot egy szabály blokkolja a hálózati elérési úton, a következő eredmény látható:

  `TCP port 445 (microsoft-ds service): FILTERED`

További információ a Portqry használatáról: [A Portqry.exe parancssori segédprogram használata](https://support.microsoft.com/help/310099).

### <a name="solution-for-cause-2"></a>Megoldás ok 2

Az informatikai részlegének véleményét megnyitni a 445-ös kimenő [Azure IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="cause-3-ntlmv1-is-enabled"></a>3. ok: NTLMv1 engedélyezve van

Rendszerhiba: 53-as vagy rendszerhiba: 87 akkor fordulhat elő, ha NTLMv1 kommunikáció engedélyezve van az ügyfélen. Az Azure Files csak NTLMv2-alapú hitelesítést támogatja. A kevésbé biztonságos ügyfél engedélyezett NTLMv1 kellene hoz létre. Ezért kommunikációja blokkolva van az Azure Files számára. 

Annak megállapításához, hogy ez-e a hiba okának, győződjön meg arról, hogy az alábbi beállításjegyzékbeli alkulcs 3 értékre van állítva:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

További információkért lásd: a [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) a témakör a TechNet webhelyén.

### <a name="solution-for-cause-3"></a>Megoldás ok 3

Visszaállítás a **LmCompatibilityLevel** érték 3 az alábbi beállításjegyzékbeli alkulcs alapértelmezett értéke:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Hiba 1816 "nincs elegendő kvótája nem érhető el, a parancs végrehajtásához" Ha az Azure-fájlmegosztás másolása

### <a name="cause"></a>Ok

1816 hiba akkor fordul elő, ha egyenlege eléri egy fájlt a számítógépen, ahol a fájlmegosztás csatlakoztatva engedélyezett egyidejű megnyitott leíróinak felső határát.

### <a name="solution"></a>Megoldás

Egyidejű megnyitott leírók számának csökkentése zárja be az egyes kezeli, és próbálkozzon újra. További információkért lásd: [a Microsoft Azure Storage teljesítmény és méretezhetőség – ellenőrzőlista](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Lassú fájl másolása és a Windows Azure-fájlokból

Lassú teljesítmény vinni a fájlokat az Azure File Storage-szolgáltatás megkísérlésekor jelenhet meg.

- Ha nem rendelkezik egy adott minimális i/o vonatkozó méretbeli követelményt, ajánlott, hogy 1 MiB, az i/o-mérete az optimális teljesítmény érdekében.
-   Ha ismeri a végleges mérethez, amely bővíti az írási műveletek egy fájl, és a szoftver nem rendelkezik kompatibilitási problémákat, ha a következő fájlon íratlan végéről nullát tartalmaz, majd állítsa be a fájl mérete előzetesen már nem kell minden írási egy kiterjesztésének írási.
-   A jobb oldali másolási módszert használják:
    -   Használat [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) bármely két fájlmegosztások közötti átvitel céljából.
    -   Használat [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) egy helyszíni számítógépen található fájlmegosztások között.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Szempontok a Windows 8.1 vagy Windows Server 2012 R2 rendszerben

Windows 8.1 vagy Windows Server 2012 R2 rendszert futtató ügyfelek győződjön meg arról, hogy a [KB3114025](https://support.microsoft.com/help/3114025) gyorsjavítás telepítve van. Ez a gyorsjavítás javítja a létrehozás, és zárja be a kezeli.

Ellenőrizze, hogy a gyorsjavítás telepítve van-e a következő szkriptet futtathatja:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Ha a gyorsjavítás telepítve van, a következő kimenet jelenik meg:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Azure Marketplace-ről Windows Server 2012 R2-rendszerképeket gyorsjavítás KB3114025 alapértelmezés szerint telepítve van a 2015 decemberében indítása rendelkezik.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>Nincs mappát a meghajtó betűjelére **Sajátgép**

Ha az Azure-fájlmegosztások rendszergazdaként hálózathasználati használatával, a megosztás úgy tűnik, hogy nem érhető el.

### <a name="cause"></a>Ok

Alapértelmezés szerint a Windows Fájlkezelőben nem futtassa rendszergazdaként. Ha hálózathasználati parancsot egy rendszergazdai parancssorból futtatja, akkor a hálózati meghajtó csatlakoztatása rendszergazdaként. Mivel a csatlakoztatott meghajtók felhasználó-központú, a felhasználói fiókkal van bejelentkezve, nem jelennek meg a meghajtók ha csatlakoztatva vannak egy másik felhasználói fiókkal.

### <a name="solution"></a>Megoldás
A megosztás nem rendszergazdai parancssorból történő csatlakoztatásához. Másik lehetőségként kövesse [a TechNet-témakör](https://technet.microsoft.com/library/ee844140.aspx) konfigurálása a **EnableLinkedConnections** beállításazonosítót.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Net use parancs sikertelen lesz, ha a tárfiók perjelet tartalmaz

### <a name="cause"></a>Ok

A net use parancs perjellel (/) kezeli, mivel a parancssori kapcsolót. Ha a felhasználói fiók nevét perjellel kezdődik, a meghajtó hozzárendelése sikertelen lesz.

### <a name="solution"></a>Megoldás

A probléma megoldásához használhatja az alábbi lépések valamelyikét:

- Futtassa az alábbi PowerShell-parancsot:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  A fájlt egy kötegfájlban futtathatja a parancsot ezzel a módszerrel:

  `Echo new-smbMapping ... | powershell -command –`

- Helyezze a kulcsot – a probléma megkerüléséhez körüli dupla idézőjelekkel együtt, kivéve, ha a perjel első karaktere. Ha igen, az interaktív mód használja, és külön adja meg a jelszót, vagy nem perjellel kezdődik a kulcs lekérése a kulcsok újragenerálása.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Alkalmazás vagy szolgáltatás nem férhetnek hozzá a csatlakoztatott Azure Files-meghajtóhoz

### <a name="cause"></a>Ok

Csatlakoztatott meghajtók felhasználónként. Ha az alkalmazás vagy szolgáltatás, amely nem a meghajtó csatlakoztatva van egy másik felhasználói fiók alatt fut, az alkalmazás nem jelenik meg a meghajtót.

### <a name="solution"></a>Megoldás

Használja az alábbi megoldások valamelyikét:

-   Csatlakoztassa a meghajtó ugyanazzal a fiókkal, amely tartalmazza az alkalmazást. Használhatja például a PsExec eszköz.
- A tárfiók nevét és a kulcsot adja meg a felhasználói nevet és a jelszó paramétereit a háló parancs használata.
- A cmdkey paranccsal adja hozzá a hitelesítő adatok hitelesítőadat-kezelő be. A szolgáltatás fiók környezetében, interaktív bejelentkezést vagy futtató használatával parancssorból hajtsa végre ezt.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Képezze le a megosztást közvetlenül csatlakoztatott meghajtóbetűjel nélkül. Egyes alkalmazások előfordulhat, hogy nem újracsatlakozás a meghajtó betűjeléhez megfelelően, így a teljes UNC elérési út használatával megbízhatóbb lehet. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Miután kövesse ezeket az utasításokat, kaphat a következő hibaüzenetet kapja a rendszer és a hálózati szolgáltatás fiók hálózathasználati futtatásakor: "1312 rendszerhiba történt. A megadott bejelentkezési munkamenet nem létezik. Azt is, hogy már befejeződött." Ha ez történik, ügyeljen arra, hogy a felhasználónév, amelyet hálózathasználati átadott tartományadatokat (például: "[tárfiók neve]. file.core.windows .net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Hiba történt ", a fájl másolásának célhelye nem támogatja a titkosítást"

A hálózaton keresztül másolja a fájlt, ha a fájl visszafejtése a forrásoldali számítógépen, egyszerű szövegként továbbított adatok köre, és újra titkosítja a célhelyen. Azonban előfordulhat, hogy lásd a következő hiba, ha egy titkosított fájl másolni kívánt: ", a fájl másolásának célhelye nem támogatja a titkosítást."

### <a name="cause"></a>Ok
Ez a probléma akkor fordulhat elő, ha titkosított fájlrendszer (EFS) használja. BitLocker-titkosítású fájlokat az Azure Files lehet másolni. Azure Files azonban nem támogatja a titkosított NTFS fájlrendszer.

### <a name="workaround"></a>Áthidaló megoldás
Fájl másolása a hálózaton keresztül, akkor először vissza kell fejtenie azt. Az alábbi módszerek valamelyikével:

- Használja a **/d másolása** parancsot. Lehetővé teszi a titkosított fájlokat, és a visszafejtett fájlokként a célhelyen.
- Állítsa be a következő beállításkulcsot:
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Érték típusa = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Érték = 1

Vegye figyelembe, hogy a beállításkulcs megadása hatással van minden, hálózati megosztások végzett másolási művelet.

## <a name="need-help-contact-support"></a>Segítség Forduljon az ügyfélszolgálathoz.
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) beolvasni a probléma gyors megoldása érdekében.
