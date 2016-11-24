---
title: "Tartomány delegálása az Azure DNS-be | Microsoft Docs"
description: "Ismerje meg, hogyan módosíthatja a tartományok delegálását és használhatja tartományszolgáltatóként az Azure DNS-névkiszolgálóit."
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/30/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 665e0684328538b61bb3eb05180d8d7d0e65ec49

---

# <a name="delegate-a-domain-to-azure-dns"></a>Tartomány delegálása az Azure DNS-be

Az Azure DNS használatával DNS-zónákat üzemeltethet, és kezelheti a tartomány DNS-rekordjait az Azure felületén. Egy tartomány DNS-lekérdezései csak akkor érik el az Azure DNS-t, ha a tartomány delegálva van az Azure DNS-be a szülőtartományból. Ne feledje: nem az Azure DNS a tartományregisztráló. Ez a cikk ismerteti a tartománydelegálás működését és a tartományok Azure DNS-be való delegálását.

## <a name="how-dns-delegation-works"></a>A DNS-delegálás működése

### <a name="domains-and-zones"></a>Tartományok és zónák

A tartománynévrendszer tartományok hierarchiájából áll. A hierarchia első eleme a „gyökértartomány”, amelynek neve egyszerűen „**.**”.  Ez alatt találhatók a legfelső szintű tartományok, mint a „com”, a „net”, az „org”, az „uk” vagy a „jp”.  Ezek alatt találhatók a másodlagos szintű tartományok, mint az „org.uk” vagy a „co.jp”.  És így tovább. A DNS-hierarchia tartományait különálló DNS-zónák üzemeltetik. A zónák globálisan fel vannak osztva, és a világ különböző pontjain található DNS-névkiszolgálók üzemeltetik őket.

**DNS-zóna**

A tartományok egyedi nevek a tartománynévrendszerben, például „contoso.com”. Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A „contoso.com” tartomány például számos DNS-rekordot tartalmazhat, például „mail.contoso.com” (levelezési kiszolgálóhoz) és „www.contoso.com” (webhelyhez).

**Tartományregisztráló**

A tartományregisztráló egy olyan cég, amely internetes tartományneveket biztosít. Ezek a cégek ellenőrzik, hogy a használni kívánt internetes tartomány elérhető-e, és ők engedélyezik azok megvásárlását. A tartománynév regisztrálása után Ön lesz annak a jogos tulajdonosa. Ha már van internetes tartománya, az aktuális tartományregisztrálóval delegálhat az Azure DNS-be.

> [!NOTE]
> További információt a tartománynevek tulajdonosairól, illetve a tartományok vásárlásáról az [Internet domain management in Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx) (Internetes tartományok kezelése az Azure AD-ben) című cikkben talál.

### <a name="resolution-and-delegation"></a>Feloldás és delegálás

Kétféle DNS-kiszolgáló létezik:

* A *mérvadó* DNS-kiszolgáló üzemelteti a DNS-zónákat. Csak az ezekben a zónákban található rekordokra irányuló DNS-lekérdezéseket válaszolja meg.
* A *rekurzív* DNS-kiszolgáló nem üzemeltet DNS-zónákat. Minden DNS-lekérdezést megválaszol a mérvadó DNS-kiszolgálók adatait összegyűjtve.

> [!NOTE]
> Az Azure DNS mérvadó DNS szolgáltatást nyújt.  Rekurzív DNS szolgáltatást nem biztosít.
>
> Az Azure felhőszolgáltatásai és virtuális gépei automatikusan egy rekurzív DNS szolgáltatás használatára vannak konfigurálva, amelyet az Azure infrastruktúra külön biztosít.  Információ ezen DNS-beállítások módosításáról: [Névfeloldás az Azure-ban](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

A számítógépes vagy mobileszköz-kompatibilis DNS-ügyfelek általában egy rekurzív DNS-kiszolgálóval végeztetik el az ügyfélalkalmazások számára szükséges DNS-lekérdezéseket.

Amikor egy rekurzív DNS-kiszolgáló egy DNS-rekordra (például „www.contoso.com”) vonatkozó lekérdezést kap, először meg kell keresnie a „contoso.com” tartomány zónáját üzemeltető névkiszolgálót. Ehhez a gyökér-névkiszolgálótól kiindulva megkeresi a „com” zónát üzemeltető névkiszolgálókat. Ezután lekérdezi a „com” névkiszolgálóktól a „contoso.com” zónát üzemeltető névkiszolgálókat.  Végül lekérdezi ezektől a névkiszolgálóktól a „www.contoso.com” címet.

Ez a művelet a DNS-név feloldása. Szigorú értelemben véve a DNS-feloldás más lépéseket is tartalmaz, például a CNAME-rekordok követését, de ez nem fontos a DNS-delegálás megértéséhez.

Hogyan „mutat rá” egy szülőzóna a gyermekzóna névkiszolgálóira? Ezt egy speciális DNS-rekorddal, az úgynevezett névkiszolgálói rekorddal hajtja végre. Például a gyökérzóna tartalmazza a „com” névkiszolgálói rekordjait, és megjeleníti a „com” zóna névkiszolgálóit. A „com” zóna pedig tartalmazza a „contoso.com” névkiszolgálói rekordjait, amelyek a „contoso.com” zóna névkiszolgálóit mutatják. Egy szülőzónán belüli gyermekzóna névkiszolgálói rekordjainak beállítását nevezzük tartománydelegálásnak.

![DNS-névkiszolgáló](./media/dns-domain-delegation/image1.png)

A delegálások a névkiszolgálói rekordok két példányával rendelkeznek: egy a gyermekzónára mutató szülőzónában, egy pedig magában a gyermekzónában található. A „contoso.com” zóna a „com” névkiszolgálói rekordjai mellett a „contoso.com” névkiszolgálói rekordjait is tartalmazza. Ezek az úgynevezett mérvadó névkiszolgálói rekordok, és a gyermekzóna tetején találhatók.

## <a name="delegating-a-domain-to-azure-dns"></a>Tartomány delegálása az Azure DNS-be
Miután létrehozta a DNS-zónáját az Azure DNS-ben, a szülőzónában be kell állítania a névkiszolgálói rekordokat, hogy az Azure DNS legyen a zóna mérvadó névfeloldási forrása. A tartományregisztrálótól vásárolt tartományokhoz a regisztráló felajánlja, hogy beállítja ezeket a névkiszolgálói rekordokat.

> [!NOTE]
> Nem kell ahhoz tartománnyal rendelkeznie, hogy azzal a tartománynévvel létrehozzon egy DNS-zónát az Azure DNS-ben. Azonban a tartományregisztrálóval az Azure DNS-be való delegáláshoz már meg kell vásárolnia a tartományt.

Tegyük fel például, hogy megvette a „contoso.com” tartományt, és létrehozott egy „contoso.com” nevű zónát az Azure DNS-ben. A tartomány tulajdonosaként a regisztráló felajánlja, hogy konfigurálja a tartomány névkiszolgálóinak címeit (azaz a névkiszolgálói rekordokat). A regisztráló ezeket a névkiszolgálói rekordokat a szülőtartományban, ebben az esetben a „.com” tartományban tárolja. A világ különböző pontjain található ügyfelek ekkor az Azure DNS zónabeli tartományához lesznek irányítva, amikor megpróbálják feloldani a „contoso.com” DNS-rekordjait.

### <a name="finding-the-name-server-names"></a>A névkiszolgálók neveinek megkeresése
Mielőtt DNS-zónáját az Azure DNS-be delegálhatná, meg kell tudnia a zóna névkiszolgálóinak neveit. Minden zóna létrehozásakor az Azure DNS egy névkiszolgálói készletből választ ki egyet.

A zónájához rendelt névkiszolgálókat legegyszerűbben az Azure-portálon tekintheti meg.  Ebben a példában a „contoso.net” zónához a következő névkiszolgálók tartoznak: „ns1-01.azure-dns.com”, „ns2-01.azure-dns.net”, „ns3-01.azure-dns.org” és „ns4-01.azure-dns.info”:

 ![DNS-névkiszolgáló](./media/dns-domain-delegation/viewzonens500.png)

Az Azure DNS automatikusan létrehozza a zóna mérvadó névkiszolgálói rekordjait, amelyek a zónához rendelt névkiszolgálókat tartalmazzák.  A névkiszolgálók neveit az Azure PowerShellen vagy az Azure parancssori felületén keresztül is megtekintheti ezeknek a rekordoknak a lekérésével.

Az Azure PowerShell-lel az alábbi módon kérheti le a mérvadó névkiszolgálói rekordokat. Vegye figyelembe, hogy a "@" nevű rekord a zóna tetején található rekordokra vonatkozik.

    PS> $zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName MyResourceGroup
    PS> Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone

    Name              : @
    ZoneName          : contoso.net
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                        ns4-01.azure-dns.info}
    Tags              : {}

Az Azure platformfüggetlen parancssori felületével is lekérheti a mérvadó névkiszolgálói rekordokat, így felfedezheti a zónájához rendelt névkiszolgálókat:

    C:\> azure network dns record-set show MyResourceGroup contoso.net @ NS
    info:    Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
    data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 172800
    data:    NS records
    data:        Name server domain name     : ns1-01.azure-dns.com.
    data:        Name server domain name     : ns2-01.azure-dns.net.
    data:        Name server domain name     : ns3-01.azure-dns.org.
    data:        Name server domain name     : ns4-01.azure-dns.info.
    data:
    info:    network dns record-set show command OK

### <a name="to-set-up-delegation"></a>Delegálás beállítása

Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. A regisztráló DNS-kezelési oldalán szerkessze a névkiszolgálói rekordokat, és cserélje le őket az Azure DNS által létrehozottakra.

Amikor egy tartományt az Azure DNS-be delegál, az Azure DNS által nyújtott névkiszolgálói neveket kell használnia.  Mindig használja mind a 4 névkiszolgálói nevet, a tartomány nevétől függetlenül.  A tartománydelegáláshoz nem szükséges, hogy a névkiszolgálói név ugyanazt a legfelső szintű tartományt használja, mint az Ön tartománya.

Ne használjon „összetartó rekordokat” az Azure DNS névkiszolgálói IP-címeire való rámutatáshoz, mert ezek az IP-címek megváltozhatnak a jövőben. A saját zónájában történő, névkiszolgálói neveket használó delegálásokat – más néven „személyes névkiszolgálókat” – az Azure DNS jelenleg nem támogatja.

### <a name="to-verify-name-resolution-is-working"></a>A névfeloldás működésének ellenőrzése

A delegálás befejezése után ellenőrizheti, hogy a névfeloldás működik-e. Ezt például az „nslookup” vagy egy hasonló eszköz segítségével teheti meg, amely lekérdezi a zónája SOA típusú rekordját (amely szintén automatikusan létrejön a zóna létrehozásakor).

Vegye figyelembe, hogy nem kell megadnia az Azure DNS névkiszolgálóit, mivel a hagyományos DNS-feloldási folyamat automatikusan megtalálja a névkiszolgálókat, ha a delegálást helyesen végezte el.

    nslookup -type=SOA contoso.com

    Server: ns1-04.azure-dns.com
    Address: 208.76.47.4

    contoso.com
    primary name server = ns1-04.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)

## <a name="delegating-sub-domains-in-azure-dns"></a>Altartományok delegálása az Azure DNS-ben

Ha különálló gyermekzónát szeretne létrehozni, azt megteheti egy altartomány Azure DNS-beli delegálásával. Tegyük fel például, hogy a „contoso.com” Azure DNS-beli beállítása és delegálása után szeretne egy különálló gyermekzónát is létrehozni, „partners.contoso.com” néven.

Az altartományok létrehozása a hagyományos delegáláshoz hasonló módon működik. Az egyetlen különbség az, hogy a 3. lépésben a névkiszolgálói rekordokat a „contoso.com” szülőzónában kell létrehozni az Azure DNS-ben, és nem a tartományregisztráló állítja be őket.

1. Hozza létre a „partners.contoso.com” gyermekzónát az Azure DNS-ben.
2. Keresse meg a mérvadó névkiszolgálói rekordokat a gyermekzónában, így megtalálja a gyermekzónát az Azure DNS-ben üzemeltető névkiszolgálókat.
3. A gyermekzónára mutató szülőzónában delegálja a gyermekzónát a névkiszolgálói rekordok konfigurálásával.

### <a name="to-delegate-a-sub-domain"></a>Altartomány delegálása

Az alábbi PowerShell-példa bemutatja ennek működését. Ugyanezek a lépések az Azure-portálon vagy az Azure platformfüggetlen parancssori felületén keresztül is végrehajthatók.

#### <a name="step-1-create-the-parent-and-child-zones"></a>1. lépés A szülő- és gyermekzónák létrehozása
Először is létre kell hozni a szülő- és a gyermekzónákat. Ezek azonos vagy eltérő erőforráscsoportokban is lehetnek.

```powershell
$parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
$child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1
```

#### <a name="step-2-retrieve-ns-records"></a>2. lépés A névkiszolgálói rekordok lékérése

Ezután le kell kérni a mérvadó névkiszolgálói rekordokat a gyermekzónából (lásd a következő példát).  Ebben találhatók a gyermekzónához rendelt névkiszolgálók.

```powershell
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS
```

#### <a name="step-3-delegate-the-child-zone"></a>3. lépés A gyermekzóna delegálása

A delegálás befejezéséhez hozzon létre egy megfelelő névkiszolgálói rekordhalmazt a szülőzónában. Vegye figyelembe, hogy a szülőzóna rekordhalmazának neve megegyezik a gyermekzóna nevével, amely ebben az esetben „partners”.

```powershell
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

### <a name="to-verify-name-resolution-is-working"></a>A névfeloldás működésének ellenőrzése

A gyermekzóna SOA típusú rekordjának megkeresésével ellenőrizheti, hogy minden helyesen van-e beállítva.

    nslookup -type=SOA partners.contoso.com

    Server: ns1-08.azure-dns.com
    Address: 208.76.47.8

    partners.contoso.com
        primary name server = ns1-08.azure-dns.com
        responsible mail addr = msnhst.microsoft.com
        serial = 1
        refresh = 900 (15 mins)
        retry = 300 (5 mins)
        expire = 604800 (7 days)
        default TTL = 300 (5 mins)

## <a name="next-steps"></a>Következő lépések

[DNS-zónák kezelése](dns-operations-dnszones.md)

[DNS-rekordok kezelése](dns-operations-recordsets.md)




<!--HONumber=Nov16_HO3-->


