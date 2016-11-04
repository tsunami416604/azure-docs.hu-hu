---
title: Azure VPN Gateway alaphelyzetbe állítása | Microsoft Docs
description: Ez a cikk útmutatást nyújt az Azure VPN Gateway alaphelyzetbe állításához. Ez a cikk a klasszikus üzemi modellel létrehozott VPN-átjárókra vonatkozik.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-service-management

ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: cherylmc

---
# Azure VPN Gateway alaphelyzetbe állítása a PowerShell használatával | Microsoft Azure
Ez a cikk útmutatást nyújt az Azure VPN Gateway PowerShell-parancsmagokkal való alaphelyzetbe állításához. Ez az útmutató a klasszikus üzemi modellre vonatkozik. A Resource Manager-alapú üzemi modellben létrehozott virtuális hálózati átjárókat jelenleg nem lehet alaphelyzetbe állítani.

Az Azure VPN Gateway alaphelyzetbe állítása akkor hasznos, ha egy vagy több S2S VPN-alagúton elveszíti a létesítmények közötti VPN-kapcsolatot. Ebben az esetben a helyszíni VPN-eszközei megfelelően működnek, de nem tudnak Ipsec-alagutakat létesíteni az Azure VPN Gateway átjárókkal. Amikor a *Reset-AzureVNetGateway* parancsmagot használja, az újraindítja az átjárót, majd újra alkalmazza arra a létesítmények közötti konfigurációkat. Az átjáró megtartja azt a nyilvános IP-címet, amellyel rendelkezik. Ez azt jelenti, hogy a VPN-útválasztó konfigurációját nem kell frissíteni új nyilvános IP-címmel az Azure VPN Gatewayhez.  

Az átjáró alaphelyzetbe állítása előtt ellenőrizze az alábbi listán szereplő legfontosabb elemeket mindegyik IPsec helyek közötti (S2S) VPN-alagúthoz. Ha bármilyen eltérés van az elemek között, az S2S VPN-alagutak kapcsolata meg fog szakadni. A helyszíni és az Azure VPN Gateway átjárók konfigurációinak ellenőrzésével és javításával elkerülhetők a szükségtelen újraindítások és az átjárók más, működő kapcsolatainak megszakadása.

Az átjáró alaphelyzetbe állítása előtt ellenőrizze a következő elemeket.

* Az Azure VPN Gateway és a helyszíni VPN-átjáró internetes IP-címei (VIP-k) helyesen vannak konfigurálva az Azure és a helyszíni VPN-házirendekben.
* Az előre megosztott kulcsnak meg kell egyeznie az Azure VPN Gateway átjáróban és a helyszíni VPN-átjáróban.
* Ha adott IPsec/IKE-konfigurációt alkalmaz – például titkosítást, kivonatoló algoritmust vagy sérülés utáni titkosságvédelmet (PFS) – ügyeljen arra, hogy az Azure VPN Gateway és a helyszíni VPN-átjárók ugyanezzel a konfigurációval rendelkezzenek.

## VPN-átjáró alaphelyzetbe állítása a PowerShell használatával
Az Azure VPN Gateway átjárók alaphelyzetbe állításához használható PowerShell-parancsmag a *Reset-AzureVNetGateway*. Minden egyes Azure VPN Gateway két virtuálisgép-példányból áll, amelyek aktív-készenléti konfigurációban futnak. A parancs kiadásakor az Azure VPN Gateway jelenleg aktív példánya azonnal újraindul. Ekkor rövid szünet következik be, amíg megtörténik a feladatátvétel az újraindítás alatt álló aktív példányról a készenléti módban lévő példányra. Ez a szünet nem tart tovább egy percnél. 

A következő példa alaphelyzetbe állítja a „ContosoVNet” nevű virtuális hálózathoz tartozó Azure VPN Gateway átjárót.

        Reset-AzureVNetGateway –VnetName “ContosoVNet” 

        Error          :
        HttpStatusCode : OK
        Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
        Status         : Successful
        RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
        StatusCode     : OK


Ha a kapcsolat nem áll vissza az első újraindítás után, adja ki ugyanezt a parancsot újra a második virtuálisgép-példány (az új aktív átjáró) újraindításához. Ha a két, újraindításra vonatkozó kérelem egymás után van kiadva, hosszabb szünet következhet be, amíg az aktív és a készenléti virtuálisgép-példány is újraindul. Ebben az esetben hosszabb szünet következik be a VPN-kapcsolatban – akár 2–4 perc –, amíg a virtuális gépek újraindulnak.

Ha a két újraindítás után a létesítmények közötti kapcsolati problémák továbbra is fennállnak, nyisson egy támogatási jegyet a klasszikus Azure portálról, amellyel felveheti a kapcsolatot a Microsoft Azure-támogatással.

## Következő lépések
A parancsmaggal kapcsolatos további információkat a [PowerShell-referenciában](https://msdn.microsoft.com/library/azure/mt270366.aspx) találhat.

<!--HONumber=sep16_HO1-->


