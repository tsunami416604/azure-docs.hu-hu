---
title: Hozzon létre egy Dual-Protocol (NFSv3 és SMB) kötetet a Azure NetApp Fileshoz | Microsoft Docs
description: Ismerteti, hogyan hozhat létre olyan kötetet, amely a NFSv3 és az SMB kettős protokollját használja az LDAP-felhasználók leképezésének támogatásával.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/15/2020
ms.author: b-juche
ms.openlocfilehash: ceaf0209dd14c8d97088d7f8e8e6990429607089
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591822"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Hozzon létre egy Dual-Protocol (NFSv3 és SMB) kötetet Azure NetApp Files

Azure NetApp Files támogatja a kötetek NFS-t (NFSv3 és NFSv 4.1), SMB3 vagy kettős protokollt használó létrehozását. Ebből a cikkből megtudhatja, hogyan hozhat létre olyan kötetet, amely a NFSv3 és az SMB kettős protokollját használja az LDAP-felhasználók leképezésének támogatásával.  


## <a name="before-you-begin"></a>Előkészületek 

* Már létre kell hoznia egy kapacitás-készletet.  
    Lásd: [Kapacitási készlet beállítása](azure-netapp-files-set-up-capacity-pool.md).   
* Az alhálózatot delegálni kell Azure NetApp Files.  
    Lásd: [alhálózat delegálása Azure NetApp Filesra](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Megfontolandó szempontok

* Győződjön meg arról, hogy megfelel a [Active Directory kapcsolatok követelményeinek](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Hozzon létre egy névkeresési zónát a DNS-kiszolgálón, majd adjon hozzá egy mutató (PTR) rekordot a névkeresési zónában található AD-gazdagéphez. Ellenkező esetben a kettős protokollú kötet létrehozása sikertelen lesz.
* Ellenőrizze, hogy az NFS-ügyfél naprakész állapotban van-e, illetve hogy az operációs rendszer legfrissebb verziója fut-e rajta.
* Győződjön meg arról, hogy a Active Directory (AD) LDAP-kiszolgáló működik és fut az AD-ben. Ezt úgy teheti meg, ha telepíti és konfigurálja a [Active Directory Lightweight Directory-szolgáltatások (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) szerepkört az ad-gépen.
* Az önaláírt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozásához és exportálásához a [Active Directory tanúsítványszolgáltatások (AD CS)](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) szerepkör használatával győződjön meg arról, hogy a hitelesítésszolgáltató (CA) létrejött az ad-ben.   
* A kettős protokollú kötetek jelenleg nem támogatják a Azure Active Directory Domain Servicest (AADDS).  
* A kettős protokollú kötet által használt NFS-verzió NFSv3. Ennek megfelelően a következő szempontokat kell figyelembe venni:
    * A kettős protokoll nem támogatja a Windows ACL-ek bővített attribútumait az `set/get` NFS-ügyfelekről.
    * Az NFS-ügyfelek nem változtathatják meg az NTFS biztonsági stílus engedélyeit, és a Windows-ügyfelek nem változtathatják meg a UNIX-stílusú kettős protokollú kötetek engedélyeit.   

    A következő táblázat ismerteti a biztonsági stílusokat és azok hatásait:  
    
    | Biztonsági stílus    | Engedélyek módosítására képes ügyfelek   | Az ügyfelek által használható engedélyek  | Eredményül kapott biztonsági stílus    | Fájlok elérésére képes ügyfelek     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3 mód BITS   | UNIX  | NFS és Windows   |
    | `Ntfs`    | Windows   | NTFS ACL-ek     | NTFS  |NFS és Windows|
* Az NTFS biztonsági stílusú kötetet az NFS használatával csatlakoztató UNIX-felhasználók Windows-felhasználóként lesznek hitelesítve a `root` UNIX `root` és `pcuser` az összes többi felhasználó számára. Győződjön meg arról, hogy ezek a felhasználói fiókok léteznek a Active Directory a kötet csatlakoztatása előtt, amikor az NFS-t használja. 


## <a name="create-a-dual-protocol-volume"></a>Kettős protokollú kötet létrehozása

1.  Kattintson a **kötetek** panelre a kapacitási készletek panelen. Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához. 

    ![A kötetek navigálása](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  A kötet létrehozása ablakban kattintson a **Létrehozás** gombra, és adja meg a következő mezők adatait az alapok lapon:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A kötet nevének egyedinek kell lennie az egyes kapacitási készleteken belül. Legalább három karakter hosszúnak kell lennie. Bármely alfanumerikus karaktert használhat.   

        `default` `bin` A vagy a kötet neve nem használható.

    * **Kapacitási készlet**  
        Határozza meg azt a kapacitási készletet, amelyben létre szeretné hozni a kötetet.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Átviteli sebesség (MiB/S)**   
        Ha a kötet kézi QoS-kapacitású készletben lett létrehozva, akkor határozza meg a kötet kívánt átviteli sebességét.   

        Ha a kötet egy automatikus QoS-kapacitási készletben jön létre, az ebben a mezőben megjelenő érték (kvóta x szolgáltatási szint átviteli sebessége).   

    * **Virtuális hálózat**  
        Itt adhatja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  

        A megadott vnet rendelkeznie kell egy Azure NetApp Files delegált alhálózattal. A Azure NetApp Files szolgáltatás csak ugyanarról a vnet érhető el, vagy egy olyan vnet, amely ugyanabban a régióban található, mint a kötet vnet-társításon keresztül. Az Express Route használatával is elérheti a kötetet a helyszíni hálózatról.   

    * **Alhálózat**  
        Itt adhatja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell Azure NetApp Files. 
        
        Ha nem delegált alhálózatot, a kötet létrehozása lapon kattintson az **új létrehozása** lehetőségre. Ezután az alhálózat létrehozása lapon adja meg az alhálózati adatokat, majd válassza a **Microsoft. NetApp/kötetek** lehetőséget az alhálózat delegálásához Azure NetApp Files számára. Minden vnet csak egy alhálózat delegálható Azure NetApp Filesra.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Ha meglévő pillanatkép-szabályzatot szeretne alkalmazni a kötetre, kattintson a **speciális szakasz megjelenítése** lehetőségre a kibontásához, adja meg, hogy el kívánja-e rejteni a pillanatkép elérési útját, majd válassza ki a pillanatkép-szabályzatot a legördülő menüben. 

        A pillanatkép-házirendek létrehozásával kapcsolatos információkért lásd: [Pillanatkép-házirendek kezelése](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Speciális kijelölés megjelenítése](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Kattintson a **protokoll** elemre, majd hajtsa végre a következő műveleteket:  
    * Válassza a **kettős protokoll (NFSv3 és SMB)** lehetőséget a kötethez.   

    * A legördülő listából válassza ki a **Active Directory** -kapcsolatokat.  
    A használt Active Directorynak rendelkeznie kell egy kiszolgáló legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványával. 

    * A kötet **kötetének elérési útját** határozza meg.   
    Ez a kötet elérési útja a megosztott kötet neve. A névnek betűvel kell kezdődnie, és az egyes előfizetésekben és régiókban egyedinek kell lennie.  

    * Határozza meg a használni kívánt **biztonsági stílust** : NTFS (alapértelmezett) vagy UNIX.

    * Szükség esetén [a kötethez tartozó exportálási szabályzatot is konfigurálhatja](azure-netapp-files-configure-export-policy.md).

    ![Kettős protokoll meghatározása](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. A kötet részleteinek áttekintéséhez kattintson a **felülvizsgálat + létrehozás** elemre. Ezután kattintson a **Létrehozás** gombra a kötet létrehozásához.

    A létrehozott kötet megjelenik a kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Active Directory hitelesítésszolgáltatói nyilvános főtanúsítvány feltöltése  

1.  A hitelesítésszolgáltató hozzáadásához és konfigurálásához kövesse [a hitelesítésszolgáltató telepítése](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) és konfigurálása című témakört. 

2.  Kövesse a [tanúsítványok megtekintése az MMC beépülő modullal](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) lehetőséget az MMC beépülő modul és a Tanúsítványkezelő eszköz használatához.  
    A Tanúsítványkezelő beépülő modullal keresse meg a helyi eszköz gyökerét vagy kiállító tanúsítványát. A Tanúsítványkezelő beépülő modul parancsait az alábbi beállítások egyikével kell futtatnia:  
    * Egy Windows-alapú ügyfél, amely csatlakozott a tartományhoz, és telepítve van a főtanúsítvány 
    * Egy másik számítógép a főtanúsítványt tartalmazó tartományban  

3. Exportálja a főtanúsítványt.  
    Győződjön meg arról, hogy a tanúsítvány az alap-64 kódolású X. 509 fájlba van exportálva (. CER) formátum: 

    ![Tanúsítvány exportálása varázsló](../media/azure-netapp-files/certificate-export-wizard.png)

4. Nyissa meg a kettős protokollú kötet NetApp-fiókját, kattintson a **Active Directory kapcsolatok** elemre, és töltse fel a legfelső szintű hitelesítésszolgáltatói tanúsítványt a **Csatlakozás Active Directory** ablak használatával:  

    ![Kiszolgáló legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa](../media/azure-netapp-files/server-root-ca-certificate.png)

    Győződjön meg arról, hogy a hitelesítésszolgáltató nevét fel lehet oldani a DNS-sel. Ez a név a tanúsítvány "kiállítója" vagy "kiállítója" mezője:  

    ![Tanúsítvány adatai](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX-attribútumok kezelése

Az Active Directory felhasználók és számítógépek MMC beépülő modullal kezelheti a POSIX-attribútumokat, például a UID-t, a kezdőkönyvtárat és az egyéb értékeket.  A következő példa a Active Directory attribútum-szerkesztőt mutatja be:  

![Active Directory Attribute Editor](../media/azure-netapp-files/active-directory-attribute-editor.png) 

A következő attribútumokat kell beállítania az LDAP-felhasználók és az LDAP-csoportok számára: 
* Az LDAP-felhasználók kötelező attribútumai:   
    `uid`: Alice, `uidNumber` : 139, `gidNumber` : 555, `objectClass` : posixAccount
* Az LDAP-csoportok szükséges attribútumai:   
    `objectClass`: "posixGroup", `gidNumber` : 555

## <a name="configure-the-nfs-client"></a>Az NFS-ügyfél konfigurálása 

Az NFS-ügyfél konfigurálásához kövesse az [NFS-ügyfél konfigurálása Azure NetApp Fileshoz](configure-nfs-clients.md) című témakör útmutatását.  

## <a name="next-steps"></a>Következő lépések  

* [NFS-ügyfél konfigurálása az Azure NetApp Fileshoz](configure-nfs-clients.md)
* [Kettős protokollú kötetek hibaelhárítása](troubleshoot-dual-protocol-volumes.md)
