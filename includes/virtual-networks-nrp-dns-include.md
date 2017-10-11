## <a name="azure-dns"></a>Azure DNS
Az Azure DNS egy olyan üzemeltetési szolgáltatás DNS-tartományok, biztosítani a névfeloldást a Microsoft Azure-infrastruktúra használatával.

| Tulajdonság | Leírás | A minta értéke |
| --- | --- | --- |
| **DNSzones** |Tartományi zóna adatainak állomás DNS-rekordok az adott tartományban |/ subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com " |

### <a name="dns-record-sets"></a>DNS-rekordhalmazok
DNS-zónák nevű rekordkészlet gyermekobjektum rendelkezik. Rekordhalmazok kivételt jelentenek a DNS-zónák típusonként állomásrekordokat gyűjteménye. Típusok A, AAAA, CNAME, MX, NS, SOA, SRV és TXT.

| Tulajdonság | Leírás | Mintaérték |
| --- | --- | --- |
| A |IPv4-rekord típusa |/Subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA |IPv6-rekord típusa |/Subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME |kanonikus név rekordtípus <sup>1</sup> |/Subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX |mail bejegyzéstípus |/Subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/MX/mail |
| NS |server type nevű |/Subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA |Szolgáltató rekordtípus kezdetét <sup>2</sup> |/Subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV |szolgáltatás bejegyzéstípus |/Subscriptions/{GUID}/.../Providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> csak lehetővé teszi, hogy egy érték / rekordhalmaz.

<sup>2</sup> csak lehetővé teszi, hogy egy rekordtípus SOA / DNS-zónát. 

Minta DNS-zóna Json formátumban:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "newZoneName": {
          "type": "String",
          "metadata": {
              "description": "The name of the DNS zone to be created."
          }
        },
        "newRecordName": {
          "type": "String",
          "defaultValue": "www",
          "metadata": {
              "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
          }
        }
      },
      "resources": 
      [
        {
          "type": "microsoft.network/dnszones",
          "name": "[parameters('newZoneName')]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": {
          }
        },
        {
          "type": "microsoft.network/dnszones/a",
          "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
          "apiVersion": "2015-05-04-preview",
          "location": "global",
          "properties": 
          {
            "TTL": 3600,
            "ARecords": 
            [
                {
                    "ipv4Address": "1.2.3.4"
                },
                {
                    "ipv4Address": "1.2.3.5"
                }
            ]
          },
          "dependsOn": [
            "[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
          ]
        }
          ]
    }

## <a name="additional-resources"></a>További források
Olvassa el a [DNS-zónák REST API dokumentációja ](https://msdn.microsoft.com/library/azure/mt130626.aspx) további információt.

Olvassa el a [DNS-rekordhalmazok REST API dokumentációja](https://msdn.microsoft.com/library/azure/mt130627.aspx) további információt.

