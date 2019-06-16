---
title: Migrálása az Azure Application Gateway webalkalmazási tűzfal v1, v2 és
description: Ez a cikk bemutatja, hogyan v2 a v1 az Azure Application Gateway és a webalkalmazási tűzfal át
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 2387f2546afa9d5af2cb909a1e6a2179548e3b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053330"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrálása az Azure Application Gateway webalkalmazási tűzfal v1, v2 és

[Az Azure Application Gateway és a webalkalmazási tűzfal (WAF) v2](application-gateway-autoscaling-zone-redundant.md) már elérhető, például az automatikus skálázás és a rendelkezésre állási zónák redundancia további funkciókat kínál. Azonban meglévő v1-átjárók nem automatikusan frissíti a v2. Ha szeretne áttelepíteni az v2 a v1, kövesse a jelen cikkben ismertetett lépések.

Az áttelepítés két szakaszból áll:

1. A konfiguráció áttelepítéséhez
2. Az ügyfél forgalmát áttelepítése

Ez a cikk ismerteti a konfiguráció-áttelepítést. Ügyfélmigrálás forgalmat az adott környezettől függően változik. Ugyanakkor magas szintű, általános ajánlásokat [biztosított](#migrate-client-traffic).

## <a name="migration-overview"></a>Migrálás – áttekintés

Az Azure PowerShell-szkript érhető el, amely a következőket:

* Létrehoz egy új Standard_v2 vagy WAF_v2 átjárót a megadott virtuális hálózat alhálózatán.
* Zökkenőmentesen másolja át a konfigurációt a v1 Standard vagy a WAF-átjáró az újonnan létrehozott Standard_V2 vagy WAF_V2 átjáró társítva.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Az új v2 átjáró új nyilvános és privát IP-címmel rendelkezik. Nem lehet áthelyezni a meglévő v1-átjáró zökkenőmentesen v2 társított IP-címeket. Azonban egy meglévő (le nem foglalt) nyilvános vagy magánhálózati IP-címet az új v2 átjáró foglalhat.
* Ahol a v1 átjáró megtalálható a virtuális hálózaton belüli IP-címtér kell adnia egy másik alhálózatot. A parancsfájl a v2-átjáró nem hozható létre, amely már rendelkezik egy v1 átjáró meglévő alhálózatokkal. Azonban ha már van a meglévő alhálózat továbbra is feltétlenül v2 az átjáró a megadott nincs elegendő IP-címtér.
* Át egy SSL-konfigurációt, meg kell adnia a v1-átjáróban használt összes SSL-tanúsítványokat.
* Ha engedélyezve van a V1 átjáró FIPS-módban van, azt az új v2-átjáró nem migrálható. FIPS-módban nem támogatott a v2-ben.
* v2 nem támogatja az IPv6-alapú, így IPv6 engedélyezett a v1-átjárók nem áttelepített. Ha futtatta a szkriptet, akkor előfordulhat, hogy nem be.
* Ha a v1 átjáró magánhálózati IP-cím, az a szkript létrehoz egy nyilvános IP-címet és az új v2 átjáró magánhálózati IP-cím. v2 átjárókat jelenleg nem támogatják a magánhálózati IP-címek.

## <a name="download-the-script"></a>A parancsprogram letöltése

Töltse le az áttelepítési parancsfájlt a [PowerShell-galériából](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>A parancsfájl használata

A helyi PowerShell-környezet beállítása és a beállítások attól függően, két lehetőség van:

* Ha még nincs telepítve a Azure Az-modulok, vagy nincs ellenére a Azure Az-modulok eltávolítása, a legjobb lehetőség használata, a `Install-Script` lehetőség a parancsfájl futtatásához.
* Ha szeretné megtartani a Azure Az-modulok, a a legjobb választás az a szkript letöltése és futtatása közvetlenül.

Ha az Azure Az olyan modulokkal rendelkezik telepített megállapításához futtassa `Get-InstalledModule -Name az`. Ha nem látja Az modulok telepítve, akkor használhatja a `Install-Script` metódust.

### <a name="install-using-the-install-script-method"></a>Telepítés a telepítési parancsfájl használatával

Használja ezt a beállítást, nem kell rendelkeznie a számítógépre telepített Azure Az-modulok. Ha vannak telepítve, a következő parancsot a hibaüzenetet jelenít meg. Távolítsa el a Azure Az-modulokat, vagy manuálisan töltse le a parancsfájlt, és futtassa azt a másik lehetőség használatával.
  
Futtassa a parancsfájlt a következő paranccsal:

`Install-Script -Name AzureAppGWMigration`

Ez a parancs is telepíti a szükséges Az modulok.  

### <a name="install-using-the-script-directly"></a>Telepítse a közvetlenül a szkripttel

Ha egyes Azure Az-modulok telepítve van, és nem lehet eltávolítani őket (vagy nem szeretné eltávolítani azokat), manuálisan letöltheti, a szkript használatával a **manuális letöltés** lapján a parancsfájl letöltésére szolgáló hivatkozásra. A parancsfájl nyers nupkg-fájlként lesz letöltve. A parancsfájl telepítése a nupkg fájlból: [manuális letöltés](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download).

A szkript futtatása:

1. Használat `Connect-AzAccount` csatlakozni az Azure-bA.

1. Használat `Import-Module Az` a Az modulok importálásához.

1. Futtatás `Get-Help AzureAppGWMigration.ps1` , vizsgálja meg a szükséges paramétereket:

   `AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale`

   A parancsfájl paramétereit:
   * **erőforrás-azonosító: [String]: Szükséges** – Ez az a meglévő Standard v1 vagy a v1-gateway WAF az Azure erőforrás-azonosítója. A karakterlánc-érték megkereséséhez nyissa meg az Azure Portalon, válassza ki az application gateway vagy a WAF-erőforrás, és kattintson a **tulajdonságok** az átjáróhoz tartozó hivatkozás. Az erőforrás-azonosító az adott oldalon található.

     Az erőforrás-azonosító beolvasásához a következő Azure PowerShell-parancsok is futtathatja:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]:  Szükséges** – Ez egy új alhálózatot, amely tartalmazza az új v2-átjáró a hozzárendelte (vagy lefoglalni kívánt) IP-címterét. Ez a CIDR jelölésrendszer szerint kell megadni. Példa: 10.0.0.0/24. Nem kell előzetesen létre ezt az alhálózatot. A parancsfájl létrehozza, ha még nem létezik.
   * **appgwName: [String]: Nem kötelező**. Ez egy karakterláncérték, adja meg a nevet az új Standard_v2 vagy WAF_v2 átjáró használatára. Ha ez a paraméter nincs megadva, a meglévő v1 átjárója nevére a utótaggal együtt használható *_v2* hozzáfűzve.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Nem kötelező**.  Az új v2-átjáróra, amely képviseli az SSL-tanúsítványokat a v1 átjáró PSApplicationGatewaySslCertificate objektumok vesszővel elválasztott listáját kell feltölteni. Az SSL-tanúsítvány konfigurálva az a szabványos v1 vagy a v1-gateway WAF mindegyike esetében hozzon létre egy új PSApplicationGatewaySslCertificate objektum keresztül a `New-AzApplicationGatewaySslCertificate` parancsot az itt látható. Az elérési utat kell az SSL-tanúsítvány fájl- és a jelszót.

       Ez a paraméter csak nem kötelező, ha nem rendelkezik a v1-átjáró vagy WAF konfigurálva HTTPS-figyelője. Ha legalább egy HTTPS-figyelő beállítása, ezt a paramétert meg kell adnia.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      Beadható `$mySslCert1, $mySslCert2` (vesszővel elválasztva) az előző példában a szkriptben a paraméter értékeként.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Nem kötelező**. PSApplicationGatewayTrustedRootCertificate Ön által létrehozott objektumok, amelyek vesszővel elválasztott listáját a [a megbízható főtanúsítványok](ssl-overview.md) a hitelesítéshez a háttér-példányok a v2-átjárón.  

      PSApplicationGatewayTrustedRootCertificate objektumlisták létrehozásához lásd: [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: Nem kötelező**. Egy adott személyes IP-címet, amely az új v2 átjáróhoz társítani szeretné.  Ez rendel az új v2-átjáró ugyanazon virtuális hálózatban kell lennie. Ha nincs megadva, a parancsfájl egy magánhálózati IP-címet a v2 átjáró foglal le.
    * **publicIpResourceId: [String]: Nem kötelező**. Az egy nyilvános IP-cím (a standard Termékváltozat) erőforrás az előfizetésében, amelyet szeretne lefoglalni az új v2 átjáróhoz resourceId. Ha nincs megadva, a parancsfájl lefoglal új nyilvános IP-cím ugyanabban az erőforráscsoportban. A v2-átjáró neve a *- IP* hozzáfűzve.
   * **validateMigration: [váltson]: Nem kötelező**. Használja ezt a paramétert, ha azt szeretné, hogy a parancsfájl a v2-átjáró létrehozása és a konfiguráció másolása után összehasonlító ellenőrzések néhány alapvető konfiguráció beállítása. Alapértelmezés szerint nem lehet érvényesíteni történik.
   * **enableAutoScale: [váltson]: Nem kötelező**. Használja ezt a paramétert, ha azt szeretné, hogy a parancsfájl automatikus skálázás engedélyezéséhez az új v2-átjáró létrehozása után. Alapértelmezés szerint le van tiltva az automatikus skálázás. Mindig manuálisan engedélyezheti azt később az újonnan létrehozott v2 átjáró.

1. Futtassa a parancsfájlt a megfelelő paraméterekkel. 5 – 7 percet igénybe vehet.

    **Példa**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceName "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Ügyfél forgalmát áttelepítése

Először ellenőrizze, hogy a parancsfájl sikeresen pontos-konfigurációval létrehozott új v2 átjáró át a v1-átjárón. Az Azure Portalon ellenőrizheti.

Ezenkívül küldése egy manuális tesztelési kisebb mennyiségű forgalmat a v2-átjárón.
  
Az alábbiakban néhány olyan forgatókönyvek, ahol előfordulhat, hogy kap az aktuális application gateway (Standard) ügyfél forgalmát, és az egyes javaslatok:

* **A Standard v1 vagy a v1-gateway WAF társított egyéni DNS-zóna (például contoso.com), amely az előtérbeli IP-cím (rekordok használatával) mutat**.

    Frissítheti a DNS-rekord az Standard_v2 application gateway társított előtérbeli IP- vagy DNS-címke mutasson. Az élettartam konfigurált DNS-rekordját, attól függően migrálása az új v2-átjáró a teljes ügyfél forgalmat egy ideig is eltarthat.
* **Egyéni DNS-zóna (például contoso.com), amely a DNS-címke (például: *myappgw.eastus.cloudapp.azure.com* egy CNAME rekord használatával) a v1 átjáró társított**.

   Két lehetőség közül választhat:

  * Ha az application gateway nyilvános IP-címeket használ, azt megteheti egy szabályozott részletes áttelepítési Traffic Manager-profil használatával növekményes az új v2 átjáró irányíthatja a forgalmat (súlyozott forgalom-útválasztási módszer).

    Ezt megteheti a DNS-címkéket, a v1 és v2 application Gateway átjárókon való hozzáadásával is a [Traffic Manager-profil](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method), és a Traffic Manager-tartományt (például, hogy az egyéni DNS-rekord (például www.contoso.com) CNAMEing contoso.trafficmanager.NET).
  * Vagy frissítheti az egyéni tartomány DNS-rekordját, hogy az új v2 application gateway DNS-címkéjéhez mutasson. Az élettartam konfigurált DNS-rekordját, attól függően migrálása az új v2-átjáró a teljes ügyfél forgalmat egy ideig is eltarthat.
* **Az ügyfelek csatlakoznak az előtér az application Gateway IP-cím**.

   Frissítse az ügyfeleket, hogy az újonnan létrehozott v2 application gateway társított IP-cím használata. Azt javasoljuk, hogy közvetlenül ne használjon IP-címeket. Fontolja meg a DNS-Névcímke (például yourgateway.eastus.cloudapp.azure.com) az application gateway, hogy a CNAME a saját egyéni DNS-zónák (például contoso.com) is társítva.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Vannak-e az Azure PowerShell-parancsprogram áttelepíti a konfigurációt a v1, v2 korlátozott?

Igen. Lásd: [figyelmeztetések/korlátozások](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Ez a cikk és az Azure PowerShell-szkript milyen esetben fizetendő, valamint az Application Gateway WAF-termékre? 

Igen.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Nem az Azure PowerShell-szkript is váltson át az újonnan létrehozott v2 átjáróhoz v1 átjáróm adatforgalma?

Nem. Az Azure PowerShell-szkript csak áttelepíti a konfigurációt. Tényleges forgalom áttelepítési az Ön felelőssége, és Önön múlik.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Van az új v2-átjáróhoz, az Azure PowerShell-szkript által létrehozott megfelelő a méretezése kezeli az összes jelenleg a v1-átjáró által kiszolgált hálózati forgalom?

Az Azure PowerShell-szkript egy új v2 átjárót hoz létre egy megfelelő méretet, a meglévő v1-átjárón a forgalom kezeléséhez. Az automatikus skálázás alapértelmezés szerint le van tiltva, de engedélyezheti az automatikus skálázás, a parancsfájl futtatásakor.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Konfiguráltam az Azure storage-naplók elküldése a v1 átjáróm. A parancsfájl replikálása nem ezt a konfigurációt, valamint a v2?

Nem. A parancsfájl nem replikálja a v2-ben Ez a konfiguráció. Hozzá kell adnia a folyamatnapló-konfiguráció külön-külön az áttelepített v2-átjáróra.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Néhány problémákba ütközik a parancsfájl futtattam. Hogyan kaphatok segítséget?
  
Egy e-mailek küldhet appgwmigrationsup@microsoft.com, nyisson meg egy támogatási kérést az Azure-támogatási vagy mindkettőt.

## <a name="next-steps"></a>További lépések

[További információ az Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
