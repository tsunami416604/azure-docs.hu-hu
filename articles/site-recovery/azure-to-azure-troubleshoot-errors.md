---
title: Az Azure Site Recovery hibaelhárítása az Azure – Azure replikációval kapcsolatos problémákat és hibákat |} A Microsoft Docs
description: Vész-helyreállítási Azure-beli virtuális gépek replikálása hibáinak és problémáinak elhárítása
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 4d8ba44cdd5161a1a5ff108837cb57af4cd98835
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034799"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure – Azure virtuális gép replikálási problémák elhárítása

Ez a cikk az Azure Site Recoveryben replikálása és helyreállítása Azure-beli virtuális gépek egyik régióból egy másik régióba gyakori problémákat ismerteti, és azok megoldását ismerteti. Támogatott konfigurációk kapcsolatos további információkért lásd: a [támogatási mátrixa Azure virtuális gépek replikálása](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Hibák listája
- **[Azure-erőforrás-kvótával kapcsolatos problémák (hibakód: 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Megbízható legfelső szintű tanúsítványok (hibakód: 151066)](#trusted-root-certificates-error-code-151066)**
- **[Kimenő kapcsolat a Site Recoveryhoz (hibakód: 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure-erőforrás-kvótával kapcsolatos problémák (hibakód: 150097)
Az előfizetés engedélyezni kell a célrégióban a vészhelyreállítási régióban használni kívánt Azure-beli virtuális gépek létrehozásához. Emellett az előfizetés meghatározott méretű virtuális gépek létrehozásához engedélyezett kvóta elegendő kell rendelkeznie. Alapértelmezés szerint a Site Recovery a forrásoldali virtuális Géppel megegyező méretű a cél virtuális gép választja ki. Ha a megfelelő méret nem érhető el, a legközelebbi lehetséges mérete automatikusan követi. Ha nincs megfelelő mérete, amely támogatja a forrás virtuális gép konfigurációs van, ez a hibaüzenet jelenik meg:

**Hibakód:** | **Lehetséges okok** | **Az ajánlás**
--- | --- | ---
150097<br></br>**Üzenet**: Nem sikerült engedélyezni a replikációt a virtuális gép VmName. | – Az előfizetés-azonosító nem engedélyezhetők olyan virtuális gépek létrehozása a célhelyen régióban.</br></br>– Az előfizetési azonosító nincs engedélyezve, vagy nem rendelkezik elegendő kvótával a célhelyen régió adott Virtuálisgép-méretek létrehozására.</br></br>-A megfelelő cél virtuális gép méretét, amely megegyezik a forrás virtuális gép hálózati adapter száma (2) az előfizetés-azonosító nem található a célhelyen régióban.| Kapcsolattartó [Azure számlázási ügyfélszolgálatát](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) a szükséges Virtuálisgép-méretek a célhelyen az előfizetéshez tartozó virtuális gépek létrehozásának engedélyezéséhez. Miután engedélyezte, próbálkozzon újra a sikertelen műveletet.

### <a name="fix-the-problem"></a>A probléma megoldása
Felveheti a kapcsolatot [Azure számlázási ügyfélszolgálatát](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) hozhat létre a szükséges méretű virtuális gépeket a célhelyen az előfizetés engedélyezése érdekében.

Ha a célhelyen kapacitás korlátozást tartalmaz, tiltsa le a replikációt, és engedélyezi azt egy másik helyre, ahol az előfizetése elegendő kvótával rendelkezik a szükséges méretű virtuális gépek létrehozásához.

## <a name="trusted-root-certificates-error-code-151066"></a>Megbízható legfelső szintű tanúsítványok (hibakód: 151066)

Ha a legújabb megbízható főtanúsítványok nem találhatók megtalálható a virtuális Gépen, a "replikáció engedélyezése" feladat meghiúsulhat. A tanúsítványok nélkül Ha hitelesítését és engedélyezését a virtuális gép Site Recovery szolgáltatás hívás sikertelen. A sikertelen "replikáció engedélyezése" Site Recovery feladat a hibaüzenet jelenik meg:

**Hibakód:** | **Lehetséges ok** | **Javaslatok**
--- | --- | ---
151066<br></br>**Üzenet**: A Site Recovery konfigurálása nem sikerült. | A szükséges megbízható főtanúsítványok engedélyezési és a hitelesítés nem található a gépen. | – A Windows operációs rendszert futtató virtuális gép, győződjön meg arról, hogy a megbízható főtanúsítványok megtalálhatók-e a gépen. További információ: [konfigurálása a megbízható főtanúsítványok és a nem engedélyezett tanúsítványok](https://technet.microsoft.com/library/dn265983.aspx).<br></br>– A Linux operációs rendszert futtató virtuális gép esetén kövesse a Linux operációs rendszer verzió terjesztője által közzétett megbízható főtanúsítványok útmutatóját.

### <a name="fix-the-problem"></a>A probléma megoldása
**Windows**

A legújabb Windows frissítések telepítése a virtuális gépen, hogy az összes megbízható legfelső szintű tanúsítványok találhatók a gépen. Ha Ön leválasztott környezetben, hajtsa végre a szabványos Windows frissítési folyamatot a szervezet tanúsítványok beolvasása. Ha a szükséges tanúsítványok nem megtalálható a virtuális Gépen, a Site Recovery szolgáltatás felé irányuló biztonsági okokból nem sikerült.

Kövesse a szokásos Windows update management vagy a tanúsítvány frissítéskezelési folyamat a szervezetben a legújabb főtanúsítványok és a frissített visszavont tanúsítványok listájának lekérése a virtuális gépeken.

Győződjön meg arról, hogy a probléma megoldódott, lépjen a virtuális gépen egy böngészőből login.microsoftonline.com.

**Linux**

Kövesse az útmutatást, a Linux-terjesztőn úgy szerezheti be a legújabb megbízható főtanúsítványok és a legfrissebb visszavonttanúsítvány-listát a virtuális gép által biztosított.

Mivel a SuSE Linux symlinks tanúsítvány listának a karbantartására használ, kövesse az alábbi lépéseket:

1.  Jelentkezzen be gyökérszintű felhasználóként.

2.  Módosítsa a könyvtárat a következő parancs futtatásával.

      ``# cd /etc/ssl/certs``

1. Ellenőrizze, hogy ha a Symantec legfelső szintű Hitelesítésszolgáltatói tanúsítvány telepítve lett-e.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Ha a Symantec legfelső szintű Hitelesítésszolgáltatói tanúsítvány nem található, a következő parancsot a fájl letöltéséhez. Keressen hibákat, és hajtsa végre a javasolt művelet hálózati hibák.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Ellenőrizze, hogy a Baltimore legfelső szintű Hitelesítésszolgáltatói tanúsítvány megtalálható.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Ha a Baltimore legfelső szintű Hitelesítésszolgáltatói tanúsítvány nem található, töltse le a tanúsítványt.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Ellenőrizze, hogy ha a DigiCert_Global_Root_CA tanúsítvány telepítve lett-e.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Ha a DigiCert_Global_Root_CA nem található, a következő parancsokat a tanúsítvány letöltése.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. A tanúsítvány frissítése rehash parancsprogram futtatásával az újonnan letöltött tanúsítványok a tulajdonos kivonatokat.

    ``# c_rehash``

8.  Ellenőrizze, hogy ha a tulajdonos kivonatolja a tanúsítványok symlinks létrehozásakor.

    - Parancs

      ``# ls -l | grep Baltimore``

    - Kimenet

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Parancs

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Kimenet

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Parancs

      ``# ls -l | grep DigiCert_Global_Root``

    - Kimenet

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Hozzon létre egy másolatot a fájlról VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem filename b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Hozzon létre egy másolatot a fájlról Baltimore_CyberTrust_Root.pem filename 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Hozzon létre egy másolatot a fájlról DigiCert_Global_Root_CA.pem filename 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Ellenőrizze, hogy megtalálhatók-e a fájlokat.  

    - Parancs

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Kimenet

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>A Site Recovery URL-címek vagy IP-címtartományokat (hibakód: 151037 vagy 151072) kimenő kapcsolatok

A Site Recovery replikációja, a kimenő kapcsolat az adott URL-címek vagy IP-címtartományok szükség a virtuális gépről. Ha a virtuális gép tűzfal mögött található, vagy használja a hálózati biztonsági csoport (NSG) szabályai kimenő kapcsolat szabályozásához, előfordulhat, hogy között ezek a problémák egyike.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>1. probléma: Nem sikerült regisztrálni az Azure-beli virtuális gépet Site Recovery (151195) </br>
- **Lehetséges ok** </br>
  - A DNS-feloldási hiba miatt nem lehet kapcsolódni Site Recovery végpontokhoz.
  - Ez gyakran látható ismételt védelem során a virtuális gép feladatátadása, de a DNS-kiszolgáló nem érhető el a DR régióban.

- **Felbontás**
   - Ha egyéni DNS használ, akkor ügyeljen arra, hogy a DNS-kiszolgáló érhető el a vész-helyreállítási régióban. Ha egy egyéni DNS nyissa meg a virtuális gép rendelkezik-e > vész-helyreállítási hálózat > DNS-kiszolgálók. Próbálja ki a virtuális gépről a DNS-kiszolgáló eléréséhez. Ha nem érhető el ezt követően testre is elérhető-e a DNS-kiszolgáló feladatátvétele vagy a sor közötti DR hálózati és a DNS-hely létrehozása.

    ![COM-hiba](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>2\. probléma: Site Recovery konfiguráció nem sikerült (151196)
- **Lehetséges ok** </br>
  - Az Office 365 portál és identitás IP4 végpontok nem létesíthető kapcsolat.

- **Felbontás**
  - Az Azure Site Recovery-hitelesítéshez szükséges hozzáférés az Office 365 IP-címek tartományát.
    Ha az Azure hálózati biztonsági csoport (NSG) szabályai, illetve a tűzfal proxy segítségével szabályozza a kimenő hálózati kapcsolatokra a virtuális Gépen, győződjön meg arról, Office 365 IP-tartományokkal való kommunikáció engedélyezése. Hozzon létre egy [Azure Active Directory (AAD) szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) alapú Hálózatibiztonságicsoport-szabály engedélyezi a hozzáférést az aad-hez tartozó összes IP-címek számára
      - Hozzáadja az új címeket az Azure Active Directory (AAD) a jövőben, ha szeretne létrehozni új NSG-szabályokat.

> [!NOTE]
> Ha a virtuális gépek a **standard** belső terheléselosztó mögött vannak, akkor nem férnek hozzá a O365 IP-címekhez, azaz Alapértelmezés szerint login.microsoftonline.com. Módosítsa az alapszintű belső terheléselosztó típusára, vagy hozzon létre egy kötött hozzáférést a [cikkben](https://aka.ms/lboutboundrulescli)említettek szerint.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>3\. probléma: Site Recovery konfiguráció nem sikerült (151197)
- **Lehetséges ok** </br>
  - Nem lehet kapcsolatot az Azure Site Recovery szolgáltatási végpontjait.

- **Felbontás**
  - Az Azure Site Recovery szükséges hozzáférést [Site Recovery IP-címtartományok](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) a régiójától függően. Győződjön meg arról, hogy a szükséges ip-címtartományok érhetők el a virtuális gép.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>4\. probléma: A A2A replikációja meghiúsult, ha a hálózati forgalom a helyszíni proxykiszolgálón halad át (151072)
- **Lehetséges ok** </br>
  - Az egyéni proxybeállítások érvénytelenek, és Azure Site Recovery mobilitási szolgáltatás ügynöke nem tudta automatikusan felderíteni a proxybeállításokat az IE-ből


- **Felbontás**
  1. A mobilitási szolgáltatás ügynökének a proxybeállításokat az Internet Explorer a Windows és Linux rendszeren /etc/environment észleli.
  2. Ha inkább a proxyt szeretné beállítani Azure Site Recovery mobilitási szolgáltatáshoz, akkor a proxy részleteit a ProxyInfo. conf fájlban adhatja meg a következő helyen:</br>
     - ``/usr/local/InMage/config/`` a ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` a ***Windows***
  3. A ProxyInfo.conf kell a proxybeállításokat a következő INI-formátumban.</br>
                *[proxy]*</br>
                *Cím =http://1.2.3.4*</br>
                *Port = 567*</br>
  4. Azure Site Recovery mobilitási szolgáltatás ügynöke csak a nem ***hitelesített proxykat***támogatja.


### <a name="fix-the-problem"></a>A probléma megoldása
[A szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-tartományok](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)engedélyezéséhez kövesse a [hálózatkezelési útmutató dokumentum](site-recovery-azure-to-azure-networking-guidance.md)lépéseit.

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>A lemez nem található a gépen (hibakód: 150039)

Egy új lemezt a virtuális Géphez csatolt inicializálni kell.

**Hibakód:** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
150039<br></br>**Üzenet**: Az Azure-adatlemez (DiskName) (DiskURI) és a logikai egység (LUN) (LUNValue) nem lett leképezve egy, a virtuális gépen belülről jelentett lemezre, amely azonos LUN-értékkel rendelkezik. | – Egy új adatlemezt a virtuális géphez lett csatolva, de azt nem lett inicializálva.</br></br>-Az adatlemezt a virtuális gép megfelelően nem jelent meg a LUN-értékkel, amelyen a lemez a virtuális géphez lett csatolva.| Győződjön meg arról, hogy az adatlemezek inicializálása, és próbálkozzon újra a művelettel.</br></br>Windows esetén: [Új lemez csatolása és inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Linux esetén: [Új adatlemez inicializálása Linuxon](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>A probléma megoldása
Győződjön meg arról, hogy az adatlemezek inicializálása megtörtént, és próbálkozzon újra a művelettel:

- Windows esetén: [Új lemez csatolása és inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Linux esetén: [lemez hozzáadása egy új data Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Ha a probléma tartósan fennáll, forduljon az ügyfélszolgálathoz.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>Legalább egy lemez (ek) védelmet biztosít (hibakód: 153039)
- **Lehetséges ok** </br>
  - Ha a védelem után egy vagy több lemezt nemrégiben adtak hozzá a virtuális géphez. 
  - Ha egy vagy több lemez a virtuális gép védelme után később lett inicializálva.

### <a name="fix-the-problem"></a>A probléma megoldása
Dönthet úgy, hogy a lemezek védelme mellett dönt, vagy figyelmen kívül hagyja a figyelmeztetést, hogy a virtuális gép replikációjának állapota ismét kifogástalan legyen.</br>
1. A lemez (ek) védelemmel. Navigáljon a replikált elemek > virtuális gép > lemezek > kattintson a nem védett lemezre > a replikáció engedélyezése lehetőségre.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. A figyelmeztetés elvetéséhez. Nyissa meg a replikált elemeket > virtuális gépen > kattintson a riasztás mellőzése elemre az Áttekintés szakaszban.
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>Távolítsa el a virtuális gépet a tárolóból, és információkkal fejeződött be (hibakód: 150225)
A virtuális gép védelmének időpontjában Azure Site Recovery hoz létre néhány hivatkozást a forrás virtuális gépen. Ha eltávolítja a védelmet vagy letiltja a replikálást, Azure Site Recovery távolítsa el ezeket a hivatkozásokat a karbantartási feladatok részeként. Ha a virtuális gépnek van erőforrás-zárolása, akkor a feladatot a rendszer az információkkal együtt teljesíti. Azt jelzi, hogy a virtuális gép el lett távolítva a Recovery Services-tárolóból, de az elavult hivatkozások némelyikét nem sikerült törölni a forrás gépről.

Figyelmen kívül hagyhatja ezt a figyelmeztetést, ha később soha nem kívánja ezt a virtuális gépet védelemmel ellátni. Ha azonban ezt a virtuális gépet később el kell végeznie, akkor az alábbi lépésekben leírtaknak megfelelően törölni kell a hivatkozásokat. 

**Ha nem végzi el a karbantartást, akkor:**

1.  A helyreállítási tár használatával történő replikáció engedélyezése során a virtuális gép nem jelenik meg. 
2.  Ha a **virtuális gépet a virtuálisgép-> beállításainak** használatával próbálja meg > a vész-helyreállítást, a "replikáció nem engedélyezhető a virtuális gépen lévő*elavult erőforrás-hivatkozások miatt*" hibaüzenettel meghiúsul.


### <a name="fix-the-problem"></a>A probléma megoldása

>[!NOTE]
>
>Azure Site Recovery nem törli a forrásként szolgáló virtuális gépet, vagy semmilyen módon nem befolyásolja a lépéseket az alábbi lépések végrehajtása során.
>

1. Szüntesse meg a virtuális gép vagy a virtuálisgép-erőforráscsoport zárolását. Példa: A (z) "MoveDemo" virtuális gép neve alatt található az erőforrás-zárolás, amelyet törölni kell.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Parancsfájl letöltése [elavult Azure site Recovery konfiguráció eltávolítása](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Futtassa a *cleanup-Stale-ASR-config-Azure-VM. ps1*parancsfájlt.
4. Adja meg az előfizetés AZONOSÍTÓját, a virtuálisgép-erőforráscsoportot és a virtuális gép nevét paraméterként.
5. Ha az Azure-beli hitelesítő adatokat kéri, adja meg azt, és ellenőrizze, hogy a parancsfájl meghibásodása nélkül fut-e. 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>Nem engedélyezhető a replikáció a virtuális gépen meglévő elavult erőforrás-hivatkozások miatt (150226-es hibakód)

**Ok: A virtuális gépnek elavult konfigurációja van a korábbi Site Recovery védelemtől**

Az elavult konfiguráció maradhat egy Azure virtuális gépen a következő esetekben:

- Engedélyezte az Azure-beli virtuális gép replikálását Site Recovery használatával, majd letiltja a replikálást, a **forrás virtuális gép pedig erőforrás-zárolással rendelkezik**.
- Engedélyezve van a replikáció az Azure virtuális gép Site Recovery használatával és anélkül, hogy explicit módon letiltja a virtuális gép replikációs törli a Site Recovery-tárból.
- Engedélyezve van a replikáció az Azure virtuális gép Site Recovery használatával, és törölt anélkül, hogy explicit módon a replikáció a virtuális gép letiltása a Site Recovery-tároló tartalmazó erőforráscsoportot.

### <a name="fix-the-problem"></a>A probléma megoldása

>[!NOTE]
>
>Azure Site Recovery nem törli a forrásként szolgáló virtuális gépet, vagy semmilyen módon nem befolyásolja a lépéseket az alábbi lépések végrehajtása során.


1. Ha vannak ilyenek, távolítsa el a virtuális gép vagy a virtuálisgép-erőforráscsoport zárolását. *Példa:* A (z) "MoveDemo" virtuális gép neve alatt található az erőforrás-zárolás, amelyet törölni kell.
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Parancsfájl letöltése [elavult Azure site Recovery konfiguráció eltávolítása](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Futtassa a *cleanup-Stale-ASR-config-Azure-VM. ps1*parancsfájlt.
4. Adja meg az előfizetés AZONOSÍTÓját, a virtuálisgép-erőforráscsoportot és a virtuális gép nevét paraméterként.
5. Ha az Azure-beli hitelesítő adatokat kéri, adja meg azt, és ellenőrizze, hogy a parancsfájl meghibásodása nélkül fut-e.  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Nem lehet megtekinteni az Azure-beli virtuális gépet vagy erőforráscsoportot a "replikáció engedélyezése" beállításhoz

 **1. ok:  Az erőforráscsoport és a forrás virtuális gép eltérő helyen található**
 
Azure Site Recovery jelenleg arra kötelezi, hogy a forrás-és a virtuális gépeknek ugyanazon a helyen kell lenniük. Ha ez nem így van, akkor a védelem idején nem fogja tudni megkeresni a virtuális gépet vagy az erőforráscsoportot. 

**Áthidaló megoldásként**engedélyezheti a replikációt a virtuális gépről a Recovery Services-tároló helyett. Lépjen a forrás virtuális gép > Tulajdonságok > vész-helyreállítás lehetőséget, és engedélyezze a replikálást.

**2. ok: Az erőforráscsoport nem része a kijelölt előfizetésnek**

Meg nem találja az erőforráscsoport a védelem idején, ha nem az adott előfizetés részeként. Győződjön meg arról, hogy az erőforráscsoport tartozik-e az előfizetés, amely használatban van.

 **3. ok: Elavult konfiguráció**
 
Ha nem látja a virtuális gép replikációs engedélyezni szeretné, akkor előfordulhat, hogy egy elavult a Site Recovery-konfiguráció miatt maradhat az Azure virtuális gépen. Az elavult konfiguráció maradhat egy Azure virtuális gépen a következő esetekben:

- Engedélyezve van a replikáció az Azure virtuális gép Site Recovery használatával és anélkül, hogy explicit módon letiltja a virtuális gép replikációs törli a Site Recovery-tárból.
- Engedélyezve van a replikáció az Azure virtuális gép Site Recovery használatával, és törölt anélkül, hogy explicit módon a replikáció a virtuális gép letiltása a Site Recovery-tároló tartalmazó erőforráscsoportot.

- Engedélyezte az Azure-beli virtuális gép replikálását Site Recovery használatával, majd letiltja a replikálást, a forrás virtuális gép pedig erőforrás-zárolással rendelkezik.

### <a name="fix-the-problem"></a>A probléma megoldása

> [!NOTE]
>
> Az alábbi szkript használata előtt frissítse a "" AzureRM. Resources "" modult. Azure Site Recovery nem törli a forrásként szolgáló virtuális gépet, vagy semmilyen módon nem befolyásolja a lépéseket az alábbi lépések végrehajtása során.
>

1. Ha vannak ilyenek, távolítsa el a virtuális gép vagy a virtuálisgép-erőforráscsoport zárolását. *Példa:* A (z) "MoveDemo" virtuális gép neve alatt található az erőforrás-zárolás, amelyet törölni kell.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Parancsfájl letöltése – [elavult konfiguráció eltávolítása](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Futtassa a *cleanup-Stale-ASR-config-Azure-VM. ps1*parancsfájlt.
4. Adja meg az előfizetés AZONOSÍTÓját, a virtuálisgép-erőforráscsoportot és a virtuális gép nevét paraméterként.
5. Ha az Azure-beli hitelesítő adatokat kéri, adja meg azt, és ellenőrizze, hogy a parancsfájl meghibásodása nélkül fut-e.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Válassza ki a virtuális gép védelemre nem sikerült
 **1. ok:  A virtuális gép egy bizonyos bővítménye sikertelen vagy nem válaszoló állapotban van.** <br>
 Lépjen a virtuális gépek > Beállítás > bővítmények és annak ellenőrzése, hogy minden olyan bővítmények hibás állapotban vannak. Távolítsa el a sikertelen bővítményt, és próbálkozzon újra a virtuális gép védelmét.<br>
 **2. ok:  [A virtuális gép kiépítési állapota érvénytelen.](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Virtuális gép kiépítési állapota nem érvényes (hibakód: 150019)

Ahhoz, hogy a gép replikációját, a kiépítési állapotot kell **sikeres**. Az alábbi lépéseket követve ellenőrizheti a virtuális gép állapota.

1.  Válassza ki a **erőforrás-kezelő** a **minden szolgáltatás** az Azure Portalon.
2.  Bontsa ki a **előfizetések** listában, és válassza ki az előfizetését.
3.  Bontsa ki a **ResourceGroups** listában, és válassza ki az erőforráscsoportot a virtuális gép.
4.  Bontsa ki a **erőforrások** listájára és válassza ki a virtuális gép
5.  Ellenőrizze a **provisioningState** példányait tartalmazó nézet jobb oldali mezőbe.

### <a name="fix-the-problem"></a>A probléma megoldása

- Ha **provisioningState** van **sikertelen**, forduljon az ügyfélszolgálathoz hibaelhárítása adatokkal.
- Ha **provisioningState** van **Updating**, egy másik bővítmény sikerült első üzembe helyezhető. Annak ellenőrzése, hogy minden folyamatban lévő műveletek a virtuális Gépen, várja meg, hogy végezze el, és próbálkozzon újra a sikertelen Site Recovery **engedélyezze a replikációt** feladat.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Nem sikerült jelölje ki a cél virtuális hálózat - hálózat kiválasztása lapon szürkén jelenik meg.

**1. ok: Ha a virtuális gép olyan hálózathoz van csatlakoztatva, amely már le van képezve egy "Target Network"-hálózatra.**
- Ha a forrásoldali virtuális gép virtuális hálózat része, és a egy másik virtuális gépről az azonos virtuális hálózatban már le van képezve egy hálózati célerőforrás-csoportban, majd a alapértelmezett hálózat kiválasztása legördülő lista letiltásra kerül.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**2. ok: Ha korábban a virtuális gépet a Azure Site Recovery használatával védte, és letiltotta a replikálást.**
 - A virtuális gépek replikáció letiltása nem törli a Hálózatleképezés. A recovery Services-tároló, amelyben a virtuális gép védett ből törlendő rendelkezik. </br>
 Lépjen a recovery Services-tároló > Site Recovery-infrastruktúra > hálózatleképezés. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - A vész-helyreállítási telepítése során konfigurált célhálózat után a kezdeti beállítása, a virtuális gép védelme után is módosítható. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Vegye figyelembe, hogy a hálózatleképezés módosítása hatással van az összes, adott hálózatleképezés használó virtuális gépek nem védett.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ hiba a kötet árnyékmásolata szolgáltatás (hibakód: 151025)

**Hibakód:** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
151025<br></br>**Üzenet**: Site Recovery bővítmény telepítése nem sikerült | – "A COM + System Application" szolgáltatás le van tiltva.</br></br>– "A kötet árnyékmásolata" szolgáltatás le van tiltva.| Állítsa a "COM + System Application" és "Kötet árnyékmásolata" szolgáltatásokat, automatikus vagy kézi indításúra.

### <a name="fix-the-problem"></a>A probléma megoldása

Nyissa meg a "Szolgáltatások" konzolt, és győződjön meg, hogy a "COM + System Application" és "Kötet árnyékmásolata" nincsenek megadva "Letiltva" "Indítási típus".
  ![COM-hiba](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nem támogatott felügyelt lemez mérete (hibakód: 150172)


**Hibakód:** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
150172<br></br>**Üzenet**: Nem sikerült engedélyezni a virtuális gép védelmét, mert a (DiskName) mérete (DiskSize) kisebb, mint a minimálisan támogatott 1024 MB-os méret. | – A lemez kisebb, mint a támogatott 1024 MB-os méret| Győződjön meg arról, hogy a lemezek mérete a támogatott tartományon belül van, majd próbálja megismételni a műveletet.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>A védelem engedélyezése nem sikerült, mert a GRUB-konfigurációban az UUID helyett az eszköz neve szerepel (hibakód: 151126)

**Lehetséges ok:** </br>
A GRUB konfigurációs fájljai ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/GRUB2/grub.cfg" vagy "/etc/default/grub") tartalmazhatják a paraméterek **gyökerének** értékeit, és a tényleges eszköznév helyett az UUID értéket. Site Recovery a mandátumok UUID-alapú megközelítését, mivel az eszközök neve a virtuális gép újraindításakor változhat, mivel a virtuális gép nem fog ugyanazzal a névvel a feladatátvétel során felmerülni, ami problémákat okoz. Példa: </br>


- A következő sor a GRUB-fájl **/boot/GRUB2/grub.cfg**származik. <br>
  *Linux/boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **resume =/dev/sda1** Splash = csendes csendes showopts*


- A következő sor a grub fájl **/boot/grub/menu.lst**
  *kernel/boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **resume =/dev/sda1** Splash = Silent crashkernel = 256M-: 128M showopts VGA = 0x314*

Ha betartja a fenti félkövér sztringet, a GRUB a "root" és a "Resume" paraméterekhez a "root" és a "Folytatás" paraméterrel rendelkezik.

**A javítás módja:**<br>
Az eszközök nevét a megfelelő UUID-val kell helyettesíteni.<br>


1. A "blkid \<eszköznév >" parancs végrehajtásával keresse meg az eszköz UUID-azonosítóját. Példa:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>
   ```



1. Most cserélje le az eszköz nevét az UUID formátumára, például: "root = UUID\<= UUID >". Ha például a "/boot/GRUB2/grub.cfg", a "/boot/GRUB2/grub.cfg" vagy a "/etc/default/grub:" fájlban a fentiekben említettek szerint az eszközök neveit az UUID értékre cseréljük, akkor a fájlok sorai úgy néznek ki, mint. <br>
   *kernel/boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** Splash = Silent crashkernel = 256M-: 128M showopts VGA = 0x314*
1. Indítsa újra a védelmet

## <a name="enable-protection-failed-as-device-mentioned-in-the-grub-configuration-doesnt-existerror-code-151124"></a>A védelem engedélyezése nem sikerült, mert a GRUB-konfigurációban említett eszköz nem létezik (hibakód: 151124)
**Lehetséges ok:** </br>
A GRUB konfigurációs fájljai ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/GRUB2/grub.cfg" vagy "/etc/default/grub") tartalmazhatják a "rd.lvm.lv" vagy "rd_LVM_LV" paramétereket, amelyek jelzik azt az LVM-eszközt, amelyet a rendszerindítás indításakor fel kell deríteni. Ha ezek az LVM-eszközök nem léteznek, a védett rendszer nem indul el és nem ragadja meg a rendszerindítási folyamatot. A feladatátvételi virtuális géppel még ugyanezt is megfigyelheti. Néhány példa:

Néhány példa: </br>

1. A következő sor a RHEL7 **"/boot/GRUB2/grub.cfg"** nevű grub-fájljából származik. </br>
   *linux16/vmlinuz-3.10.0-957.el7.x86_64 root =/dev/Mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet lang = en_US. UTF-8*</br>
   Itt a kiemelt rész azt mutatja, hogy a GRUB-nak két, **"root"** és **"swap"** nevű LVM eszközt kell észlelni a (z) "rootvg" kötetből.
1. A következő sor a **"/etc/default/grub"** grub-fájlból származik a RHEL7 </br>
   *GRUB_CMDLINE_LINUX = "crashkernel = Auto **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet"*</br>
   Itt a kiemelt rész azt mutatja, hogy a GRUB-nak két, **"root"** és **"swap"** nevű LVM eszközt kell észlelni a (z) "rootvg" kötetből.
1. A következő sor a **"/boot/grub/menu.lst"** grub-fájlból származik a 64 bites rhel6 </br>
   *kernel/vmlinuz-2.6.32-754.el6.x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = automatikus rd_LVM_LV = rootvg/lv_root KEYBOARDTYPE = PC-s tábla = US rd_LVM_LV = rootvg/lv_swap rd_NO_DM rhgb csendes* </br>
   Itt a kiemelt rész azt mutatja, hogy a GRUB-nak két, **"root"** és **"swap"** nevű LVM eszközt kell észlelni a (z) "rootvg" kötetből.<br>

**A javítás módja:**<br>

Ha az LVM-eszköz nem létezik, javítsa ki vagy távolítsa el a (z) paramétert a GRUB konfigurációs fájljaival, majd próbálja meg újra engedélyezni a védelmet. </br>

## <a name="site-recovery-mobility-service-update-completed-with-warnings--error-code-151083"></a>Site Recovery mobilitási szolgáltatás frissítése figyelmeztetésekkel fejeződött be (hibakód: 151083)
Site Recovery mobilitási szolgáltatásnak számos összetevője van, amelyek közül az egyik neve szűrő-illesztőprogram. A szűrő-illesztőprogram csak a rendszer újraindításakor lesz betöltve a rendszermemóriába. Ha vannak olyan Site Recovery mobilitási szolgáltatás frissítései, amelyek szűrő-illesztőprogram-módosításokkal rendelkeznek, a rendszer frissíti a gépet, de továbbra is figyelmezteti, hogy egyes javítások újraindítást igényelnek. Ez azt jelenti, hogy a szűrő-illesztőprogram javításai csak akkor valósíthatók meg, ha új szűrő-illesztőprogram töltődik be, ami csak a rendszer újraindításakor történhet meg.<br>
Vegye **figyelembe** , hogy ez csak egy figyelmeztetés, és a meglévő replikáció az új ügynök frissítése után is továbbra is működik. Dönthet úgy, hogy bármikor újraindítja az új szűrő-illesztőprogram előnyeit, de ha nem indul újra, mint a régi szűrő-illesztőprogram, továbbra is működik. A szűrő-illesztőprogramon kívül a **mobilitási szolgáltatás bármely más fejlesztésének és javításának előnyeit az ügynök frissítése nélkül is megvalósíthatja.**  


## <a name="protection-couldnt-be-enabled-as-replica-managed-disk-diskname-replica-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Nem sikerült engedélyezni a védelmet, mert a (z) "diskname-replika" replika felügyelt lemez már létezik, a célként megadott erőforráscsoport nem várt címkék nélkül (hibakód: 150161

**Ok**: Ez akkor fordulhat elő, ha a virtuális gép korábban a múltban volt védve, és a replikáció letiltása során a replika lemezét valamilyen ok miatt nem tisztították meg.</br>
**A javítás módja:** Törölje az említett replika lemezét a hibaüzenetben, majd indítsa újra a sikertelen védelmi feladatot.

## <a name="next-steps"></a>További lépések
[Azure-alapú virtuális gépek replikálása](site-recovery-replicate-azure-to-azure.md)
