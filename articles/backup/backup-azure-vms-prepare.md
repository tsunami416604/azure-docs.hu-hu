---
title: A környezet biztonsági mentése Azure virtuális gépek előkészítése |} Microsoft Docs
description: Győződjön meg arról, hogy a környezet készen áll a testreszabásra az Azure virtuális gépek biztonsági mentéséről
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonn
editor: ''
keywords: biztonsági mentések; biztonsági mentése;
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/10/2017
ms.author: cwatson
ms.openlocfilehash: 03bb4c3e68643ded686c20b7ba0d5d079a9d4057
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>A környezet előkészítése Azure-beli virtuális gépek biztonsági mentéséhez
> [!div class="op_single_selector"]
> * [Erőforrás-kezelő modell](backup-azure-arm-vms-prepare.md)
> * [Klasszikus modell](backup-azure-vms-prepare.md)
>
>

Készíthet biztonsági mentést egy Azure virtuális gép (VM), mielőtt nincsenek három feltétel, amely már léteznie kell.

* Hozzon létre egy mentési tárolót, vagy egy meglévő biztonsági mentési tárolót azonosító *ugyanabban a régióban, mint a virtuális gép*.
* Az Azure nyilvános Internet-címek és az Azure storage végpontok közötti hálózati kapcsolatot létesíteni.
* A Virtuálisgép-ügynök telepítése a virtuális gépen.

Ha tudja, hogy ezek a feltételek már létezik a környezetben, majd folytassa a [készítsen biztonsági másolatot a virtuális gépek cikk](backup-azure-vms.md). Ellenkező esetben olvasni, ez a cikk végigvezeti a környezet biztonsági mentése az Azure virtuális gép előkészítése a lépéseket.

##<a name="supported-operating-system-for-backup"></a>Támogatott operációs rendszer biztonsági mentése
 * **Linux**: Az Azure Backup az [Azure által támogatott disztribúciókat](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) támogatja, a Core OS Linux kivételével. _Más kerüljön-a-saját-Linux terjesztéseket is előfordulhat, hogy működni, amíg a Virtuálisgép-ügynök érhető el a virtuális gépen, a Python létezik támogatása. Azonban azt hitelesíti ezeket terjesztéseket, a biztonsági mentéshez._
 * **Windows Server**: A Windows Server 2008 R2-nél régebbi verziók nem támogatottak.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Ha a biztonsági mentése és visszaállítása egy virtuális gép korlátozásai
> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Az alábbi lista ismerteti a korlátozások vonatkoznak, a klasszikus modellben való telepítésekor.
>
>

* Több mint 16 adatlemezekkel rendelkező virtuális gépek biztonsági mentését nem támogatott.
* A fenntartott IP-cím és a nem definiált végpontot a virtuális gépek biztonsági mentését nem támogatott.
* Biztonsági mentési adatok nem tartalmazza a virtuális Géphez csatlakozik, a csatlakoztatott hálózati meghajtókat.
* Egy meglévő virtuális gép cseréje a visszaállítás során nem támogatott. Először törölje a meglévő virtuális gép és a kapcsolódó lemezzel, és ezután adatok visszaállítása biztonsági másolatból.
* Kereszt-régió biztonsági mentése és visszaállítása nem támogatott.
* Azure minden nyilvános régiókban támogatott virtuális gépek biztonsági mentését az Azure Backup szolgáltatás segítségével (lásd a [ellenőrzőlista](https://azure.microsoft.com/regions/#services) a támogatott régiók). A régiót, amelyben keres jelenleg nem támogatott, ha már nem jelenik a legördülő lista tároló létrehozása során.
* Virtuális gépek biztonsági mentését az Azure Backup szolgáltatás használatával csak select operációs rendszereken támogatott:
* A tartományvezérlők visszaállítását (DC) virtuális Gépet, amely része egy multi-tartományvezérlő-konfiguráció támogatott csak a PowerShell segítségével. Tudjon meg többet az [multi-DC tartományvezérlő visszaállítása](backup-azure-restore-vms.md#restoring-domain-controller-vms).
* Az alábbi speciális beállításokkal rendelkező virtuális gépek visszaállításakor csak a PowerShell használatával támogatott. A visszaállítási munkafolyamat a felhasználói felület használatával létrehozott virtuális gépek mindaddig nem lesz a hálózati konfigurációt, a visszaállítási művelet befejeződése után. További tudnivalókért lásd: [visszaállítását virtuális gépek speciális hálózati konfigurációkkal](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Virtuális gépek a terheléselosztó-konfigurációja (belső és külső)
  * Virtuális gépek több foglalt IP-címmel
  * Virtuális gépek több hálózati adapterrel

## <a name="create-a-backup-vault-for-a-vm"></a>A virtuális gépek biztonsági mentési tároló létrehozása
A Backup-tároló egy olyan entitás, amely tárolja az idők során létrehozott biztonsági mentéseket és helyreállítási pontokat. A mentési tároló is tartalmaz a biztonsági mentési házirendek, amelyek érvényesek a virtuális gépek biztonsági mentése folyamatban.

> [!IMPORTANT]
> 2017 márciusától már nem hozhat létre Backup-tárolókat a klasszikus portálon. A már meglévő Backup-tárolók továbbra is támogatottak, és az [Azure PowerShell használatával létrehozhat Backup-tárolókat](./backup-client-automation-classic.md#create-a-backup-vault). A Microsoft azonban azt javasolja, hogy Recovery Services-tárolókat hozzon létre az összes üzemelő példányhoz, mivel a jövőbeli fejlesztések csak a Recovery Services-tárolókra vonatkoznak majd.


A képen látható a különböző Azure biztonsági mentés entitások közötti kapcsolatok: ![Azure biztonsági mentési entitásokat és kapcsolatok](./media/backup-azure-vms-prepare/vault-policy-vm.png)



## <a name="network-connectivity"></a>Hálózati kapcsolat
A virtuális gép pillanatképek kezeléséhez, a tartalék mellék kapcsolódnia kell az Azure nyilvános IP-címeket. A jobb oldali internetkapcsolat nélkül a virtuális gép HTTP-kérelmek túllépi az időkorlátot, és a biztonsági mentés sikertelen lesz. Ha a központi telepítés rendelkezik korlátozza a hozzáférést (a hálózati biztonsági csoport (NSG), például), majd válassza ki biztosítani azt egy tiszta elérési utat a biztonsági mentési forgalom ezen beállítások valamelyikét:

* [Engedélyezett az Azure datacenter IP-címtartományok](http://www.microsoft.com/en-us/download/details.aspx?id=41653) -talál utasításokat a hogyan számára engedélyezett az IP-címek.
* A forgalom útválasztási HTTP-proxy kiszolgáló telepítése.

Amikor arról dönt, hogy melyik lehetőséget használja, a kompromisszumot kezelhetőség, a tanúsítványhasználat pontos szabályzása és költsége közötti vannak.

| Beállítás | Előnyei | Hátrányok |
| --- | --- | --- |
| Engedélyezett IP-címtartományok |Nincsenek további költségek.<br><br>A hozzáférést egy NSG, használja a <i>Set-AzureNetworkSecurityRule</i> parancsmag. |A befolyásolt kezelése bonyolult IP-címtartományok változnak az idők.<br><br>Azure és az nem csak a teljes hozzáférést biztosít. |
| HTTP-proxy |Részletes szabályozását a proxyval, a tárolási engedélyezett URL-címeket. A telepítő a tanúsítványhasználat pontos szabályzása a proxy a https://\*.blob.core.windows.net/\* URL-minta kell lennie a szerepel az engedélyezési listán. Az engedélyezési lista csak a tárfiók a virtuális gép által használt https://\<storageAccount\>.blob.core.windows.net/\* URL-minta kell lennie a szerepel az engedélyezési listán. <br>Virtuális gépek egyetlen pont, Internet-hozzáférést.<br>Nem vonatkozik a Azure IP-címet érintő módosításait. |További költségek a virtuális gépek futtatásához a proxy szoftverrel. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Engedélyezett Azure datacenter IP-címtartományok
Az engedélyezési lista az Azure datacenter IP-címtartományok, tekintse meg a [Azure-webhelyen](http://www.microsoft.com/en-us/download/details.aspx?id=41653) talál részletes információt az IP-címtartományok, és az utasításokat.

### <a name="using-an-http-proxy-for-vm-backups"></a>A virtuális gép biztonsági mentésekhez egy HTTP-proxy használatával
Ha a virtuális gépek biztonsági mentéséről, a biztonsági mentési bővítményt a virtuális Gépen a pillanatkép felügyeleti parancsokat küld Azure Storage egy HTTPS API használatával. A tartalék mellék forgalmat a HTTP-proxyn keresztül történő irányításához, mivel ez a nyilvános Internet-hozzáférés beállítása csak összetevő.

> [!NOTE]
> Nincs a proxy szoftver használandó nem ajánlott. Győződjön meg arról, hogy a proxy, amely rendelkezik a kimenő tölcsérútvonalak választja, és amely megfelel az alábbi konfigurációs lépéseket. Győződjön meg arról, hogy a harmadik féltől származó szoftverekkel ne módosítsa a proxykiszolgáló beállításait
>
>

Az alábbi példa képen lépéseit mutatja be, a három kvórumbeállítási szükséges HTTP proxyk használatára:

* App virtuális gép továbbítja a HTTP-forgalom a nyilvános interneten keresztül Proxy Virtuálisgép kapcsolódik.
* Proxy VM lehetővé teszi a bejövő forgalom virtuális gépek a virtuális hálózat.
* A hálózati biztonsági csoport (NSG) nevű Elégtelen-zárolási kell egy biztonsági szabály engedélyezése kimenő internetforgalom Proxy virtuális gépről.

![NSG a HTTP-proxy telepítési diagram](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

A nyilvános internethez való kommunikációhoz HTTP proxyk használatára, kövesse az alábbi lépéseket:

#### <a name="step-1-configure-outgoing-network-connections"></a>1. lépés A kimenő hálózati kapcsolatok konfigurálása
###### <a name="for-windows-machines"></a>A Windows-alapú gépek
Ez a helyi rendszerfiókhoz proxykiszolgálót állítják be.

1. Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Futtassa a következő parancs rendszergazda jogú parancssorból

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Megnyílik az internet explorer ablakot.
3. Ugrás a eszközök -> Internet-beállítások -> kapcsolatok LAN-beállítások ->.
4. Ellenőrizze a proxybeállítások helyességét a rendszerfiók. Állítsa be a Proxy IP-cím és port.
5. Zárja be az Internet Explorert.

Beállításához nyújt útmutatást a gépre kiterjedő proxy konfigurációját, és egyetlen kimenő HTTP/HTTPS-forgalmat fog történni.

Ha a telepítő proxykiszolgálót a jelenlegi felhasználói fiók (nem a helyi rendszerfiók), használja a következő parancsfájl SYSTEMACCOUNT alkalmazni:

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Ha "(407) Proxyhitelesítés szükséges" proxy server naplóban, ellenőrizze a hitelesítési helyesen beállítva.
>
>

###### <a name="for-linux-machines"></a>Linux-gépekhez
Adja hozzá a következő sort a ```/etc/environment``` fájlt:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Adja hozzá a következő sorokat a ```/etc/waagent.conf``` fájlt:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>2. lépés A proxykiszolgáló bejövő kapcsolatok engedélyezése:
1. Nyissa meg a proxykiszolgáló, a Windows tűzfal. A legegyszerűbben úgy lehet hozzáférni a tűzfal fokozott biztonságú Windows tűzfal kereséséhez.

    ![Nyissa meg a tűzfal](./media/backup-azure-vms-prepare/firewall-01.png)
2. A Windows tűzfal párbeszédpanelen kattintson a jobb gombbal **bejövő szabályok** kattintson **új szabály létrehozása...** .

    ![Új szabály létrehozása](./media/backup-azure-vms-prepare/firewall-02.png)
3. Az a **új bejövő szabály varázsló**, válassza ki a **egyéni** választás, a **szabálytípus** kattintson **következő**.
4. A lapon válassza ki a **Program**, válassza a **minden program** kattintson **következő**.
5. Az a **protokoll és portok** lapon adja meg a következő adatokat, majd kattintson **következő**:

    ![Új szabály létrehozása](./media/backup-azure-vms-prepare/firewall-03.png)

   * a *protokolltípus* válasszon *TCP*
   * a *helyi port* válasszon *adott*, az alábbi mezőben adja meg a ```<Proxy Port>``` be van állítva.
   * a *távoli port* válasszon *minden port*

     A varázsló többi egészen a érdekében kattintson, és nevezze el ez a szabály.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>3. lépés Vegyen fel egy kivételt a az NSG:
Az Azure PowerShell-parancssort írja be a következő parancsot:

Az alábbi parancs hozzáadja az NSG kivételt. Ez a kivétel lehetővé teszi, hogy a TCP-forgalom 10.0.0.5 a bármely portról bármely internetcím a 80-as (HTTP) vagy a 443-as (HTTPS) porton. Ha a nyilvános internethez az adott portra van szüksége, ügyeljen arra, hogy, hogy a port hozzáadása az ```-DestinationPortRange``` is.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

*Győződjön meg arról, hogy az adott telepítéshez megfelelő adatokkal cserélje le a példában szereplő nevét.*

## <a name="vm-agent"></a>Virtuálisgép-ügynök
Előtt készíthet biztonsági mentést az Azure virtuális géphez, meg kell győződnie arról, hogy az Azure Virtuálisgép-ügynök helyesen van telepítve a virtuális gép. Mivel a Virtuálisgép-ügynök választható összetevője, amikor a virtuális gép létrehozása, győződjön meg arról, hogy a Virtuálisgép-ügynök a jelölőnégyzetet a virtuális gép kiépítése előtt van-e kiválasztva.

### <a name="manual-installation-and-update"></a>Manuális telepítés és frissítés
A Virtuálisgép-ügynök már szerepel az Azure katalógusában a létrehozott virtuális gépeket. A helyszíni adatközpontját a áttelepített virtuális gépek azonban nem áll a Virtuálisgép-ügynök van telepítve. Ilyen virtuális gépek a Virtuálisgép-ügynök telepítve kell lennie explicit módon. 

| **Művelet** | **Windows** | **Linux** |
| --- | --- | --- |
| A virtuálisgép-ügynök telepítése |Töltse le és telepítse az [ügynök MSI-t](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek.<li>[Frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), hogy megjelenjen a telepített ügynök. |<li> Telepítse a legújabb [Linux-ügynök](../virtual-machines/extensions/agent-linux.md). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. Azt javasoljuk, hogy a terjesztési tárházból ügynök telepítése. A Microsoft **nem javasoljuk** közvetlenül a githubból telepítése Linux Virtuálisgép-ügynök.  |
| A virtuálisgép-ügynök frissítése |A virtuálisgép-ügynök frissítése a [virtuálisgép-ügynök bináris fájljainak](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) újratelepítéséből áll. <br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |Kövesse a [linuxos virtuálisgép-ügynök frissítését](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ismertető szakasz utasításait. Azt javasoljuk, hogy a terjesztési tárházból ügynök frissítése. A Microsoft **nem javasoljuk** közvetlenül a githubból frissítése Linux Virtuálisgép-ügynök.<br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |
| A virtuálisgép-ügynök telepítésének érvényesítése |<li>Lépjen a *C:\WindowsAzure\Packages* mappába az Azure virtuális gépen. <li>Itt találja a WaAppAgent.exe fájlt.<li> Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A Termék verziószáma mezőben 2.6.1198.718 vagy újabb verziónak kell lennie. |– |

További tudnivalók a [Virtuálisgép-ügynök](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) és [telepítéséről](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### <a name="backup-extension"></a>Backup bővítmény
Biztonsági mentése a virtuális gépet, az Azure Backup szolgáltatás telepítése egy bővítményt a Virtuálisgép-ügynök. Az Azure Backup szolgáltatás akadásmentesen frissíti és javítja a biztonsági mentési bővítményt a felhasználó további beavatkozása nélkül.

A tartalék mellék telepítve van, ha a virtuális gép fut-e. A virtuális gép is biztosít a legnagyobb valószínűséggel az alkalmazáskonzisztens helyreállítási pont első. Azonban az Azure Backup szolgáltatás továbbra is készítsen biztonsági másolatot a virtuális gép – még akkor is, ha ki van kapcsolva, és a bővítmény nem sikerült telepíteni (más néven Offline virtuális gép). Ebben az esetben a helyreállítási pont lesz *összeomlás-konzisztens* kapcsolatban a fentiekben ismertetett módon.

## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>További lépések
Most, hogy előkészítette a környezetet az biztonsági mentése a virtuális Gépet, a következő logikai lépésre biztonsági mentés létrehozásához. A tervezési cikk virtuális gépek biztonsági mentéséről további részletes információkat tartalmazza.

* [Virtuális gépek biztonsági mentése](backup-azure-vms.md)
* [A virtuális gép biztonsági mentési infrastruktúra megtervezése](backup-azure-vms-introduction.md)
* [Virtuális gépek biztonsági mentéseinek kezelése](backup-azure-manage-vms.md)
