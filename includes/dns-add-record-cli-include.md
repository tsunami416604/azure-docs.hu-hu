#### Egyetlen rekordot tartalmazó A típusú rekordhalmaz létrehozása

Rekordhalmaz létrehozásához használja a következőt: `azure network dns record-set create`. Adja meg az erőforráscsoportot, a zóna nevét, a rekordhalmaz relatív nevét, a rekord típusát, valamint az élettartamát (TTL).

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Az A-rekordhalmaz létrehozása után adja hozzá az IPv4-címet a halmazhoz ezzel: `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### Egyetlen rekordot tartalmazó AAAA típusú rekordhalmaz létrehozása

    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### Egyetlen rekordot tartalmazó CNAME típusú rekordhalmaz létrehozása

A CNAME-rekordok csak egyetlen karakterláncérték használatát engedélyezik.


    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### Egyetlen rekordot tartalmazó MX típusú rekordhalmaz létrehozása

Ebben a példában a „@” rekordhalmaznevet használjuk az MX-rekord zóna felső pontjánál történő létrehozásához (amely ebben az esetben: contoso.com). Ez gyakori az MX-rekordok esetében.

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### Egyetlen rekordot tartalmazó NS típusú rekordhalmaz létrehozása

    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### Egyetlen rekordot tartalmazó SRV típusú rekordhalmaz létrehozása

Ha a zóna gyökerénél hoz létre SRV típusú rekordot, a „_service” és „_protocol” elemeket megadhatja a rekord nevében. A „@”-t nem szükséges belefoglalni a rekord nevébe.


    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### Egyetlen rekordot tartalmazó TXT típusú rekordhalmaz létrehozása

    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"


<!--HONumber=Sep16_HO3-->


