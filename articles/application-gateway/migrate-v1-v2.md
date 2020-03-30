---
title: Áttelepítés a v1-ről a v2-re – Azure Application Gateway
description: Ez a cikk bemutatja, hogyan telepítheti át az Azure Application Gateway és a Web Application Firewall alkalmazást a v1-ről a v2-re
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9909c46015fffb3bea3eef094599312e28b935c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77046201"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Az Azure Application Gateway és a webalkalmazás tűzfalának áttelepítése a v1-ről a v2-re

[Az Azure Application Gateway és a Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) már elérhető, amely további funkciókat kínál, például az automatikus skálázásés a rendelkezésre állási zóna redundanciája. A meglévő 1-es verziójú átjárók frissítése a 2-es verzióra azonban nem történik meg automatikusan. Ha a v1-ről a v2-re szeretne áttérni, kövesse a cikkben ismertetett lépéseket.

Az áttelepítésnek két szakasza van:

1. A konfiguráció áttelepítése
2. Az ügyfélforgalom áttelepítése

Ez a cikk a konfiguráció áttelepítésével foglalkozik. Az ügyfélforgalom áttelepítése az adott környezettől függően változik. Ugyanakkor néhány magas szintű, általános ajánlások [is rendelkezésre állnak.](#migrate-client-traffic)

## <a name="migration-overview"></a>Áttelepítés – áttekintés

Egy Azure PowerShell-parancsfájl érhető el, amely a következőket teszi:

* Új Standard_v2 vagy WAF_v2 átjárót hoz létre a megadott virtuális hálózati alhálózatban.
* Zökkenőmentesen másolja a v1 Standard vagy WAF átjáróhoz társított konfigurációt az újonnan létrehozott Standard_V2 vagy WAF_V2 átjáróra.

### <a name="caveatslimitations"></a>Kikötések\Korlátozások

* Az új v2 átjáró új nyilvános és privát IP-címekkel rendelkezik. Nem lehet áthelyezni a meglévő v1 átjáróhoz társított IP-címeket zökkenőmentesen a v2-be. Azonban egy meglévő (fel nem osztott) nyilvános vagy privát IP-címet is lefoglalhat az új v2 átjáróhoz.
* Meg kell adnia egy IP-címterületet egy másik alhálózat számára a virtuális hálózaton belül, ahol a v1 átjáró található. A parancsfájl nem hozhat létre a v2 átjáró t olyan meglévő alhálózatokban, amelyek már rendelkeznek v1-es átjáróval. Azonban ha a meglévő alhálózat már rendelkezik egy v2-átjáróval, amely továbbra is működhet, feltéve, hogy elegendő IP-címterület áll rendelkezésre.
* SSL-konfiguráció áttelepítéséhez meg kell adnia a v1 átjáróban használt összes SSL-tanúsítványt.
* Ha a V1-átjáró fips-módban engedélyezve van, akkor nem lesz áttelepítve az új v2 átjáróra. A FIPS-mód nem támogatott a v2-ben.
* A v2 nem támogatja az IPv6 protokollt, ezért az IPv6-kompatibilis v1-es átjárók áttelepítése nem történik meg. Ha futtatja a parancsfájlt, előfordulhat, hogy nem fejeződik be.
* Ha a v1-es átjáró csak egy privát IP-címmel rendelkezik, a parancsfájl létrehoz egy nyilvános IP-címet és egy privát IP-címet az új v2 átjáróhoz. a v2 átjárók jelenleg nem csak a privát IP-címeket támogatják.

## <a name="download-the-script"></a>A szkript letöltése

Töltse le az áttelepítési parancsfájlt a [PowerShell-galériából.](https://www.powershellgallery.com/packages/AzureAppGWMigration)

## <a name="use-the-script"></a>A szkript használata

A helyi PowerShell-környezet beállításától és beállításaitól függően két lehetőség közül választhat:

* Ha nincs telepítve az Azure Az-modulok, vagy nem bánja az Azure Az-modulok eltávolítását, a `Install-Script` legjobb megoldás a parancsfájl futtatásához.
* Ha meg kell tartania az Azure Az modulokat, a legjobb, ha letölti a parancsfájlt, és közvetlenül futtatja.

Annak megállapításához, hogy telepítve vannak-e `Get-InstalledModule -Name az`az Azure Az modulok, futtassa a futtassa a futtassa a futtassa a programot. Ha nem látja a telepített Az modulokat, akkor `Install-Script` használhatja a módszert.

### <a name="install-using-the-install-script-method"></a>Telepítés a Install-Script metódussal

A beállítás használatához nem kell az Azure Az modulok telepítve a számítógépre. Ha telepítve vannak, a következő parancs hibát jelenít meg. Eltávolíthatja az Azure Az modulokat, vagy a másik lehetőséggel manuálisan letöltheti a parancsfájlt, és futtathatja azt.
  
Futtassa a parancsfájlt a következő paranccsal:

`Install-Script -Name AzureAppGWMigration`

Ez a parancs a szükséges Az modulokat is telepíti.  

### <a name="install-using-the-script-directly"></a>Telepítés közvetlenül a parancsfájl használatával

Ha néhány Azure Az-modul telepítve van, és nem tudja eltávolítani őket (vagy nem szeretné eltávolítani őket), manuálisan letöltheti a parancsfájlt a parancsfájl **letöltési hivatkozásának Kézi letöltés** elapjával. A szkript letölthető, mint egy nyers nupkg fájlt. Ha erről a nupkg fájlból szeretné telepíteni a parancsfájlt, olvassa el a Kézi csomag letöltése című [témakört.](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)

A szkript futtatása:

1. Az `Connect-AzAccount` Azure-hoz való csatlakozáshoz használható.

1. Az `Import-Module Az` Az modulok importálására használható.

1. Futtassa `Get-Help AzureAppGWMigration.ps1` a szükséges paraméterek vizsgálatához:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   A parancsfájl paraméterei:
   * **resourceId: [String]: Kötelező** – Ez a meglévő Standard v1 vagy WAF v1 átjáró Azure-erőforrás-azonosítója. A karakterlánc-érték megkereséséhez keresse meg az Azure Portalon, válassza ki az alkalmazásátjárót vagy a WAF-erőforrást, és kattintson az átjáró **Tulajdonságok** hivatkozására. Az erőforrás-azonosító az adott lapon található.

     Az erőforrás-azonosító lekérte a következő Azure PowerShell-parancsokat is:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]: Kötelező** – Ez az az IP-címterület, amelyet az új v2 átjárót tartalmazó új alhálózathoz lefoglalt (vagy lefoglalni szeretne). Ezt a CIDR jelölésben kell megadni. Például: 10.0.0.0/24. Nem kell előre létrehoznia ezt az alhálózatot. A szkript létrehozza az Ön számára, ha nem létezik.
   * **appgwName: [String]: Nem kötelező**. Ezt a karakterláncot az új Standard_v2 vagy WAF_v2 átjáró neveként kell használni. Ha ez a paraméter nincs megadva, a meglévő v1 átjáró nevét fogja használni az utótag *_v2* hozzáfűzve.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Nem kötelező**.  A PSApplicationGatewaySslCertificate objektumok vesszővel tagolt listáját, amelyet a v1-es átjáró SSL-tanúsítványainak ábrázolására hoz létre, fel kell tölteni az új v2 átjáróba. A standard v1 vagy WAF v1 átjáróhoz konfigurált ssl-tanúsítványok mindegyikéhez létrehozhat egy új PSApplicationGatewaySslCertificate objektumot az `New-AzApplicationGatewaySslCertificate` itt látható parancs segítségével. Szüksége van az SSL tanúsítványfájl elérési útvonalára és a jelszóra.

     Ez a paraméter csak akkor választható, ha nincs HTTPS-figyelők konfigurálva a v1 átjáró vagy a WAF. Ha legalább egy HTTPS-figyelő beállítással rendelkezik, meg kell adnia ezt a paramétert.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Az előző `$mySslCert1, $mySslCert2` példában a paraméter értékeként a d.
   * **trustedRootCertificates: [PSApplicationApplicationTrustedRootCertificate]: Nem kötelező.** A PSApplicationGatewayTrustedRootCertificate objektumok vesszővel tagolt listája, amelyet a háttérpéldányok v2 átjáróból történő hitelesítéséhez a megbízható legfelső szintű tanúsítványok at ábrázoló [megbízható legfelső szintű tanúsítványokat](ssl-overview.md) hoz létre.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      A PSApplicationApplicationTrustedRootCertificate objektumok listájának létrehozásáról a [New-AzApplicationGatewayTrustedRootCertificate című témakörben található.](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)
   * **privateIpAddress: [String]: Nem kötelező**. Egy adott privát IP-címet, amely et szeretne társítani az új v2 átjáró.  Ennek ugyanabból a virtuális hálózatból kell származnia, amelyet az új v2-átjáróhoz foglal le. Ha ez nincs megadva, a parancsfájl lefoglalja a v2 átjáró privát IP-címét.
   * **publicIpResourceId: [Karakterlánc]: Nem kötelező.** Az erőforrás-azonosító a meglévő nyilvános IP-cím (standard Termékváltozat) erőforrás az előfizetésben, amely le szeretné foglalni az új v2-átjáró. Ha ez nincs megadva, a parancsfájl új nyilvános IP-címet foglal le ugyanabban az erőforráscsoportban. A név a v2 átjáró neve *-IP* hozzáfűzve.
   * **validateMigration: [switch]: Nem kötelező**. Akkor használja ezt a paramétert, ha azt szeretné, hogy a parancsfájl a v2 átjáró létrehozása és a konfigurációs másolat létrehozása után néhány alapvető konfigurációs összehasonlítási ellenőrzést végezzen. Alapértelmezés szerint nem történik érvényesítés.
   * **enableAutoScale: [switch]: Nem kötelező**. Ezt a paramétert akkor használja, ha azt szeretné, hogy a parancsfájl engedélyezze az automatikus skálázást az új v2 átjárón a létrehozása után. Alapértelmezés szerint az automatikus skálázás le van tiltva. Az újonnan létrehozott v2-átjárón később bármikor manuálisan engedélyezheti.

1. Futtassa a parancsfájlt a megfelelő paraméterek kel. 5-7 percig is eltarthat.

    **Példa**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Ügyfélforgalom áttelepítése

Először ellenőrizze, hogy a parancsfájl sikeresen létrehozott-e egy új v2-átjárót a v1-es átjáróról áttelepített konfigurációval. Ezt az Azure Portalon ellenőrizheti.

Emellett manuális tesztként küldjön egy kis forgalmat a v2-átjárón keresztül.
  
Íme néhány forgatókönyv, ahol az aktuális alkalmazásátjáró (Standard) ügyfélforgalmat kaphat, és az egyes javaslatok:

* **Egyéni DNS-zóna (például contoso.com), amely a standard v1 vagy WAF v1 átjáróhoz társított előtér-IP-címre mutat (A rekord használatával).**

    A DNS-rekord frissítésével a Standard_v2 alkalmazásátjáróhoz társított előtér-IP- vagy DNS-címkére mutathat. A DNS-rekordon konfigurált TTL-től függően eltarthat egy ideig, amíg az összes ügyfélforgalom áttelepül az új v2 átjáróra.
* **Egyéni DNS-zóna (például contoso.com), amely a v1 átjáróhoz társított DNS-címkére mutat (például: *myappgw.eastus.cloudapp.azure.com* CNAME rekord használatával).**

   Két választásod van:

  * Ha nyilvános IP-címeket használ az alkalmazásátjárón, a Traffic Manager-profil használatával ellenőrzött, részletes áttelepítést tehet le a forgalom (súlyozott forgalomútozási módszer) növekményes irányítására az új v2-átjáróhoz.

    Ezt úgy teheti meg, `www.contoso.com`hogy a v1 és v2 alkalmazásátjárók DNS-címkéit hozzáadja a Traffic [Manager-profilhoz](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method), és az egyéni DNS-rekordot (például ) a Traffic Manager tartományhoz (például contoso.trafficmanager.net) adja hozzá.
  * Vagy frissítheti az egyéni tartomány DNS-rekordját úgy, hogy az új v2 alkalmazásátjáró DNS-címkéje jelenjen meg. A DNS-rekordon konfigurált TTL-től függően eltarthat egy ideig, amíg az összes ügyfélforgalom áttelepül az új v2 átjáróra.
* **Az ügyfelek az alkalmazásátjáró előtér-IP-címéhez csatlakoznak.**

   Frissítse az ügyfeleket az újonnan létrehozott v2 alkalmazásátjáróhoz társított IP-cím(ek) használatához. Azt javasoljuk, hogy ne használjon közvetlenül IP-címeket. Fontolja meg az alkalmazásátjáróhoz társított DNS-névcímke használatát (például yourgateway.eastus.cloudapp.azure.com), amelyet a CNAME a saját egyéni DNS-zónájához (például contoso.com) használhat.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Vannak-e korlátozások az Azure PowerShell-parancsfájl áttelepítése a konfiguráció v1 v2?

Igen. Lásd [a kifogásokat/korlátozásokat.](#caveatslimitations)

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Ez a cikk és az Azure PowerShell-parancsfájl is alkalmazható az Application Gateway WAF-termékre? 

Igen.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Az Azure PowerShell-parancsfájl is átvált a forgalmat a v1-es átjáróaz újonnan létrehozott v2 átjáró?

Nem. Az Azure PowerShell-parancsfájl csak a konfiguráció áttelepítése. A tényleges forgalom migráció az Ön felelőssége, és az Ön ellenőrzése.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Az új v2-átjáró az Azure PowerShell-parancsfájl mérete megfelelően kezeli a v1-es átjáróm által jelenleg kiszolgált összes forgalmat?

Az Azure PowerShell-parancsfájl létrehoz egy új v2-átjáró t a megfelelő méretű a meglévő v1-átjáró forgalma kezelésére. Az automatikus skálázás alapértelmezés szerint le van tiltva, de a parancsfájl futtatásakor engedélyezheti az automatikus skálázást.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Úgy állítottam be a v1-es átjárómat, hogy naplókat küldjön az Azure storage-ba. A parancsfájl replikálja ezt a konfigurációt a v2-hez is?

Nem. A parancsfájl nem replikálja ezt a konfigurációt a v2.The script doesn't replicate this configuration for v2. A naplókonfigurációt külön kell hozzáadnia az áttelepített v2 átjáróhoz.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Támogatja ez a parancsfájl az Azure KeyVaultba feltöltött tanúsítványokat?

Nem. A parancsfájl jelenleg nem támogatja a KeyVault tanúsítványait. Ez azonban egy későbbi verzió esetében is fontolóra veszi.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Belefutottam néhány probléma segítségével ezt a forgatókönyvet. Hogyan kaphatok segítséget?
  
E-mailt küldhet appgwmigrationsup@microsoft.coma, megnyithat egy támogatási esetet az Azure-támogatással, vagy mindkettőt megteheti.

## <a name="next-steps"></a>További lépések

[Tudnivalók az Application Gateway v2-ről](application-gateway-autoscaling-zone-redundant.md)
