---
title: 'Azure VPN Gateway: csomagok rögzítésének konfigurálása'
description: Ismerje meg a VPN-átjárók által használható csomag-rögzítési funkciókat.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 3ba3046367ceece6bf0ddf157451025c79977324
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077210"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>A csomagok rögzítésének konfigurálása a VPN-átjárók számára

A kapcsolat és a teljesítménnyel kapcsolatos problémák gyakran bonyolultak, és jelentős mennyiségű időt és erőfeszítést igényelnek, és csak a probléma okának csökkentése érdekében. A csomagok rögzítésének képessége nagy mértékben segít csökkenteni a probléma hatókörét a hálózat bizonyos részeire, például azt, hogy a probléma a hálózat ügyfél oldalán, a hálózat Azure-oldalán vagy valahol a között van-e. A probléma szűkítése után sokkal hatékonyabb a hibakeresés és a javító művelet végrehajtása.

Vannak általánosan elérhető eszközök a csomagok rögzítéséhez. Ezeknek az eszközöknek a megfelelő csomagjainak beszerzése nehézkes lehet, különösen nagy mennyiségű forgalmi forgatókönyvek használata esetén. A VPN Gateway-csomagok rögzítése által biztosított szűrési képességek jelentős differenciáló válnak. A VPN Gateway-csomagok rögzítését az általánosan elérhető csomagok rögzítése eszközein kívül is használhatja.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>A VPN Gateway csomagok rögzítésének szűrési képességei

A VPN Gateway-csomagok rögzítése az ügyfél igényeitől függően az átjárón vagy egy adott kapcsolaton is futtatható. A csomagok rögzítését egyszerre több alagúton is futtathatja. A VPN-átjárón való szűrés mellett egyetlen vagy kétirányú forgalmat, IKE-és ESP-forgalmat, valamint belső csomagokat is rögzíthet.

Egy öt rekordból álló szűrő (forrásoldali alhálózat, célként használt alhálózat, forrásport, célport, protokoll) és TCP-jelzők (SYN, ACK, FIN, URG, PSH, első) használata hasznos lehet a nagy mennyiségű forgalom problémáinak elkülönítéséhez.

Tekintse meg a JSON-és JSON-séma alább látható példáját az egyes tulajdonságok magyarázatával. A csomagok rögzítése során bizonyos korlátozásokat is figyelembe kell venni:
- A sémában a szűrő tömbként jelenik meg, de egyszerre csak egy szűrőt lehet használni.
- Egy időben több, az átjáróra kiterjedő csomagok rögzítése nem engedélyezett.
- Ugyanazon a kapcsolaton egyszerre több csomag is nem engedélyezett. A csomagok rögzítéseit egyszerre is futtathatja különböző kapcsolatokon.
- Egy átjárón belül legfeljebb öt csomagot lehet futtatni párhuzamosan. Ezek a csomagok az egész átjáróra kiterjedő csomagok rögzítése vagy a kapcsolati csomagok rögzítésének kombinációja lehet.

### <a name="example-json"></a>Példa JSON-ra
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>JSON-séma
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="setup-packet-capture-using-powershell"></a>Csomagok rögzítésének beállítása a PowerShell használatával

A csomagok rögzítésének elindításához és leállításához tekintse meg az alábbi példákat a PowerShell-parancsokhoz. A paraméterek beállításaival kapcsolatos további információkért tekintse meg ezt a PowerShell- [dokumentumot](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>A csomagok rögzítésének elindítása VPN-átjáró esetén

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Opcionális paraméter **– a FilterData** használható a szűrő alkalmazásához.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>A csomagok rögzítésének leállítása a VPN-átjárón

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>A csomagok rögzítésének elindítása a VPN Gateway-kapcsolathoz

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Opcionális paraméter **– a FilterData** használható a szűrő alkalmazásához.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>A csomagok rögzítésének leállítása a VPN Gateway-kapcsolaton

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Fő szempontok

- A csomagok rögzítésének futtatása hatással lehet a teljesítményre. Ne felejtse el leállítani a csomagok rögzítését, ha nincs rá szükség.
- A csomagok minimális rögzítési időtartama 600 másodperc. Előfordulhat, hogy a csomagok rögzítésének rövidebb időtartama nem biztosít teljes körű adatmennyiséget, mert az elérési út több összetevője között szinkronizálja a problémákat.
- A csomag-rögzítési adatfájlok PCAP formátumban jönnek létre. A PCAP-fájlok megnyitásához használjon Wireshark vagy más általánosan elérhető alkalmazásokat.

## <a name="next-steps"></a>További lépések

További információ a VPN Gatewayről: [about VPN Gateway](vpn-gateway-about-vpngateways.md)
