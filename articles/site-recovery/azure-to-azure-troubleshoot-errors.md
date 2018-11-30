---
title: Az Azure Site Recovery hibaelhárítása az Azure – Azure replikációval kapcsolatos problémákat és hibákat |} A Microsoft Docs
description: Vész-helyreállítási Azure-beli virtuális gépek replikálása hibáinak és problémáinak elhárítása
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: sujayt
ms.openlocfilehash: 7d11460fd1db5ba92725567a41aaaeab9e752adb
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308124"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure – Azure virtuális gép replikálási problémák elhárítása

Ez a cikk az Azure Site Recoveryben replikálása és helyreállítása Azure-beli virtuális gépek egyik régióból egy másik régióba gyakori problémákat ismerteti, és azok megoldását ismerteti. Támogatott konfigurációk kapcsolatos további információkért lásd: a [támogatási mátrixa Azure virtuális gépek replikálása](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure-erőforrás kvótával kapcsolatos problémák (hibakód: 150097)
Az előfizetés engedélyezni kell a célrégióban a vészhelyreállítási régióban használni kívánt Azure-beli virtuális gépek létrehozásához. Emellett az előfizetés meghatározott méretű virtuális gépek létrehozásához engedélyezett kvóta elegendő kell rendelkeznie. Alapértelmezés szerint a Site Recovery a forrásoldali virtuális Géppel megegyező méretű a cél virtuális gép választja ki. Ha a megfelelő méret nem érhető el, a legközelebbi lehetséges mérete automatikusan követi. Ha nincs megfelelő mérete, amely támogatja a forrás virtuális gép konfigurációs van, ez a hibaüzenet jelenik meg:

**Hibakód:** | **Lehetséges okok** | **Az ajánlás**
--- | --- | ---
150097<br></br>**Üzenet**: nem sikerült engedélyezni a virtuális gép VmName replikációját. | – Az előfizetés-azonosító nem engedélyezhetők olyan virtuális gépek létrehozása a célhelyen régióban.</br></br>– Az előfizetési azonosító nincs engedélyezve, vagy nem rendelkezik elegendő kvótával a célhelyen régió adott Virtuálisgép-méretek létrehozására.</br></br>-A megfelelő cél virtuális gép méretét, amely megegyezik a forrás virtuális gép hálózati adapter száma (2) az előfizetés-azonosító nem található a célhelyen régióban.| Kapcsolattartó [Azure számlázási ügyfélszolgálatát](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) a szükséges Virtuálisgép-méretek a célhelyen az előfizetéshez tartozó virtuális gépek létrehozásának engedélyezéséhez. Miután engedélyezte, próbálkozzon újra a sikertelen műveletet.

### <a name="fix-the-problem"></a>A probléma megoldása
Felveheti a kapcsolatot [Azure számlázási ügyfélszolgálatát](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) hozhat létre a szükséges méretű virtuális gépeket a célhelyen az előfizetés engedélyezése érdekében.

Ha a célhelyen kapacitás korlátozást tartalmaz, tiltsa le a replikációt, és engedélyezi azt egy másik helyre, ahol az előfizetése elegendő kvótával rendelkezik a szükséges méretű virtuális gépek létrehozásához.

## <a name="trusted-root-certificates-error-code-151066"></a>Megbízható legfelső szintű tanúsítványok (hibakód: 151066)

Ha a legújabb megbízható főtanúsítványok nem találhatók megtalálható a virtuális Gépen, a "replikáció engedélyezése" feladat meghiúsulhat. A tanúsítványok nélkül Ha hitelesítését és engedélyezését a virtuális gép Site Recovery szolgáltatás hívás sikertelen. A sikertelen "replikáció engedélyezése" Site Recovery feladat a hibaüzenet jelenik meg:

**Hibakód:** | **Lehetséges ok** | **Javaslatok**
--- | --- | ---
151066<br></br>**Üzenet**: a Site Recovery konfigurálása nem sikerült. | A szükséges megbízható főtanúsítványok engedélyezési és a hitelesítés nem található a gépen. | – A Windows operációs rendszert futtató virtuális gép, győződjön meg arról, hogy a megbízható főtanúsítványok megtalálhatók-e a gépen. További információ: [konfigurálása a megbízható főtanúsítványok és a nem engedélyezett tanúsítványok](https://technet.microsoft.com/library/dn265983.aspx).<br></br>– A Linux operációs rendszert futtató virtuális gép esetén kövesse a Linux operációs rendszer verzió terjesztője által közzétett megbízható főtanúsítványok útmutatóját.

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

3. Ellenőrizze, hogy ha a Symantec legfelső szintű Hitelesítésszolgáltatói tanúsítvány telepítve lett-e.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Ha a Symantec legfelső szintű Hitelesítésszolgáltatói tanúsítvány nem található, a következő parancsot a fájl letöltéséhez. Keressen hibákat, és hajtsa végre a javasolt művelet hálózati hibák.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Ellenőrizze, hogy a Baltimore legfelső szintű Hitelesítésszolgáltatói tanúsítvány megtalálható.

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Ha a Baltimore legfelső szintű Hitelesítésszolgáltatói tanúsítvány nem található, töltse le a tanúsítványt.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. Ellenőrizze, hogy ha a DigiCert_Global_Root_CA tanúsítvány telepítve lett-e.

    ``# ls DigiCert_Global_Root_CA.pem``

8. Ha a DigiCert_Global_Root_CA nem található, a következő parancsokat a tanúsítvány letöltése.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. A tanúsítvány frissítése rehash parancsprogram futtatásával az újonnan letöltött tanúsítványok a tulajdonos kivonatokat.

    ``# c_rehash``

10. Ellenőrizze, hogy ha a tulajdonos kivonatolja a tanúsítványok symlinks létrehozásakor.

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

11. Hozzon létre egy másolatot a fájlról VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem filename b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

12. Hozzon létre egy másolatot a fájlról Baltimore_CyberTrust_Root.pem filename 653b494a.0

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

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>1 probléma: Nem sikerült regisztrálni az Azure virtuális gépet a Site Recovery (151195) </br>
- **Lehetséges ok** </br>
  - A site recovery végpontok DNS-feloldási hiba miatt nem létesíthető kapcsolat.
  - Ez gyakran látható ismételt védelem során a virtuális gép feladatátadása, de a DNS-kiszolgáló nem érhető el a DR régióban.
  
- **Felbontás**
   - Ha egyéni DNS használ, akkor ügyeljen arra, hogy a DNS-kiszolgáló érhető el a vész-helyreállítási régióban. Ha egy egyéni DNS nyissa meg a virtuális gép rendelkezik-e > vész-helyreállítási hálózat > DNS-kiszolgálók. Próbálja ki a virtuális gépről a DNS-kiszolgáló eléréséhez. Ha nem érhető el ezt követően testre is elérhető-e a DNS-kiszolgáló feladatátvétele vagy a sor közötti DR hálózati és a DNS-hely létrehozása.
  
    ![COM-hiba](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)
 

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>2 probléma: A Site Recovery konfigurálása nem sikerült (151196)
- **Lehetséges ok** </br>
  - Az Office 365 portál és identitás IP4 végpontok nem létesíthető kapcsolat.

- **Felbontás**
  - Az Azure Site Recovery-hitelesítéshez szükséges hozzáférés az Office 365 IP-címek tartományát.
    Ha az Azure hálózati biztonsági csoport (NSG) szabályai, illetve a tűzfal proxy segítségével szabályozza a kimenő hálózati kapcsolatokra a virtuális Gépen, győződjön meg arról, Office 365 IP-tartományokkal való kommunikáció engedélyezése. Hozzon létre egy [Azure Active Directory (AAD) szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) alapú Hálózatibiztonságicsoport-szabály engedélyezi a hozzáférést az aad-hez tartozó összes IP-címek számára
        - Hozzáadja az új címeket az Azure Active Directory (AAD) a jövőben, ha szeretne létrehozni új NSG-szabályokat.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>3. hiba: A Site Recovery konfigurálása nem sikerült (151197)
- **Lehetséges ok** </br>
  - Nem lehet kapcsolatot az Azure Site Recovery szolgáltatási végpontjait.

- **Felbontás**
  - Az Azure Site Recovery szükséges hozzáférést [Site Recovery IP-címtartományok](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) a régiójától függően. Győződjön meg arról, hogy a szükséges ip-címtartományok érhetők el a virtuális gép.
    

### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>4. hiba: Az A2A-replikáció sikertelen, ha a hálózati forgalom halad át a helyi proxykiszolgáló (151072)
 - **Lehetséges ok** </br>
   - Érvénytelenek az egyéni proxy beállításait, és az ASR a mobilitási szolgáltatás ügynökének fejeződött nincs automatikus észlelés a proxybeállításokat az Internet Explorer


 - **Felbontás**
  1.    A mobilitási szolgáltatás ügynökének a proxybeállításokat az Internet Explorer a Windows és Linux rendszeren /etc/environment észleli.
  2.  Ha inkább a állítsa be a proxy csak az automatikus rendszer-Helyreállítás a mobilitási szolgáltatást, majd megadhatja a proxy adatait a ProxyInfo.conf helyen található:</br>
      - ``/usr/local/InMage/config/`` a ***Linux***
      - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` a ***Windows***
  3.    A ProxyInfo.conf kell a proxybeállításokat a következő INI-formátumban. </br>
                   *[proxy]*</br>
                   *Cím =http://1.2.3.4*</br>
                   *Port = 567*</br>
  4. Csak támogatja az automatikus rendszer-Helyreállítás a mobilitási szolgáltatás ügynökének ***nem hitelesített proxyk***.

### <a name="fix-the-problem"></a>A probléma megoldása
Az engedélyezési listára [a szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-címtartományok](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), kövesse a [hálózati dokumentum](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>A lemez nem található a gépen (hibakód: 150039)

Egy új lemezt a virtuális Géphez csatolt inicializálni kell.

**Hibakód:** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
150039<br></br>**Üzenet**: Azure adatlemez (DiskName) (DiskURI) rendelkező logikai egységen (LUN) (LUNValue) nincs hozzárendelve egy megfelelő lemezt a virtuális gép, amely azonos LUN-értékkel rendelkezik által jelentett. | – Egy új adatlemezt a virtuális géphez lett csatolva, de azt nem lett inicializálva.</br></br>-Az adatlemezt a virtuális gép megfelelően nem jelent meg a LUN-értékkel, amelyen a lemez a virtuális géphez lett csatolva.| Győződjön meg arról, hogy az adatlemezek inicializálása, és próbálkozzon újra a művelettel.</br></br>A Windows: [csatolás és a egy új lemez inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Linux esetén: [Linux új adatlemez inicializálása](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>A probléma megoldása
Győződjön meg arról, hogy az adatlemezek inicializálása megtörtént, és próbálkozzon újra a művelettel:

- A Windows: [csatolás és a egy új lemez inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Linux esetén: [lemez hozzáadása egy új data Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Ha a probléma tartósan fennáll, forduljon az ügyfélszolgálathoz.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Nem látja az Azure virtuális gép kiválasztása a "replikáció engedélyezése"

 **1. ok: Erőforráscsoport és a forrás virtuális gép máshol helyezkedik el** <br>
Az Azure Site Recovery jelenleg meghatalmazott, hogy régió erőforráscsoportot és a virtuális gépek ugyanazon a helyen legyen. Ha ez nem így majd akkor nem tudná az védelem alatt a virtuális gép található.

**2. ok: Erőforráscsoport nem szerepel a kiválasztott előfizetéshez** <br>
Meg nem találja az erőforráscsoport a védelem idején, ha nem az adott előfizetés részeként. Győződjön meg arról, hogy az erőforráscsoport tartozik-e az előfizetés, amely használatban van.

 **3. ok: A elavult konfiguráció** <br>
Ha nem látja a virtuális gép replikációs engedélyezni szeretné, akkor előfordulhat, hogy egy elavult a Site Recovery-konfiguráció miatt maradhat az Azure virtuális gépen. Az elavult konfiguráció maradhat egy Azure virtuális gépen a következő esetekben:

- Engedélyezve van a replikáció az Azure virtuális gép Site Recovery használatával és anélkül, hogy explicit módon letiltja a virtuális gép replikációs törli a Site Recovery-tárból.
- Engedélyezve van a replikáció az Azure virtuális gép Site Recovery használatával, és törölt anélkül, hogy explicit módon a replikáció a virtuális gép letiltása a Site Recovery-tároló tartalmazó erőforráscsoportot.

### <a name="fix-the-problem"></a>A probléma megoldása

Használhat [távolítsa el az elavult automatikus konfigurációs szkript](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) , és távolítsa el az elavult a Site Recovery konfigurálása az Azure virtuális gépen. A virtuális gép láthatja a elavult konfiguráció eltávolítása után kell lennie.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Válassza ki a virtuális gép védelemre nem sikerült 
 **1. ok: a virtuális gép rendelkezik néhány bővítmény telepítve állapota sikertelen, vagy nem válaszol** <br>
 Lépjen a virtuális gépek > Beállítás > bővítmények és annak ellenőrzése, hogy minden olyan bővítmények hibás állapotban vannak. Távolítsa el a sikertelen bővítményt, és próbálkozzon újra a virtuális gép védelmét.<br>
 **2. ok: [virtuális gép kiépítési állapota nem érvényes](#vms-provisioning-state-is-not-valid-error-code-150019)**

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

**1. ok: Ha a virtuális gép csatlakoztatva van egy hálózatot, amely a cél hálózati már hozzá van rendelve.**
- Ha a forrásoldali virtuális gép virtuális hálózat része, és a egy másik virtuális gépről az azonos virtuális hálózatban már le van képezve egy hálózati célerőforrás-csoportban, majd a alapértelmezett hálózat kiválasztása legördülő lista letiltásra kerül.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**2. ok: Ha korábban a virtuális gép az Azure Site Recovery által védett, és le van tiltva a replikáció.**
 - A virtuális gépek replikáció letiltása nem törli a Hálózatleképezés. A recovery Services-tároló, amelyben a virtuális gép védett ből törlendő rendelkezik. </br>
 Lépjen a recovery Services-tároló > Site Recovery-infrastruktúra > hálózatleképezés. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - A vész-helyreállítási telepítése során konfigurált célhálózat után a kezdeti beállítása, a virtuális gép védelme után is módosítható. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Vegye figyelembe, hogy a hálózatleképezés módosítása hatással van az összes, adott hálózatleképezés használó virtuális gépek nem védett.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ hiba a kötet árnyékmásolata szolgáltatás (hibakód: 151025)
**Hibakód:** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
151025<br></br>**Üzenet**: nem sikerült telepíteni a Site recovery-bővítmény | – "A COM + System Application" szolgáltatás le van tiltva.</br></br>– "A kötet árnyékmásolata" szolgáltatás le van tiltva.| Állítsa a "COM + System Application" és "Kötet árnyékmásolata" szolgáltatásokat, automatikus vagy kézi indításúra.

### <a name="fix-the-problem"></a>A probléma megoldása

Nyissa meg a "Szolgáltatások" konzolt, és győződjön meg, hogy a "COM + System Application" és "Kötet árnyékmásolata" nincsenek megadva "Letiltva" "Indítási típus".
  ![COM-hiba](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="next-steps"></a>További lépések
[Azure-alapú virtuális gépek replikálása](site-recovery-replicate-azure-to-azure.md)
