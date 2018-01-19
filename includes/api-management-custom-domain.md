## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Hogyan APIM Proxy kiszolgáló válaszol, a TLS-kézfogás SSL-tanúsítványok

### <a name="clients-calling-with-sni-header"></a>Az ügyfelek SNI fejléc metódust hívja.
Az ügyfél egy vagy több egyéni tartomány Proxy konfigurálva van, APIM válaszolni tud HTTPS-kéréseket az egyéni tartomány (például contoso.com) a továbbá az alapértelmezett tartomány (például apim-szolgáltatás-name.azure-api.net). A kiszolgálónév jelzése (SNI) fejléc adatai alapján, APIM válaszol, megfelelő kiszolgálói tanúsítványt.

### <a name="clients-calling-without-sni-header"></a>Az SNI-fejléc nélküli hívó ügyfelek
Az ügyfél használja-e egy ügyfél nem küldi el a [SNI](https://tools.ietf.org/html/rfc6066#section-3) fejléc, APIM hoz létre a következő logikán alapuló válaszok:

* Ha a szolgáltatás van konfigurálva Proxy a csak egy egyéni tartományt, az alapértelmezett tanúsítvány az egyéni Proxy tartományhoz korábban kiadott tanúsítványt.
* Ha a szolgáltatás több egyéni tartomány konfigurálva proxy (csak akkor támogatott a a **prémium** réteg), az ügyfél melyik tanúsítványt kell lennie az alapértelmezett tanúsítvány is kijelölhet. Az alapértelmezett tanúsítvány beállítása a [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#hostnameconfiguration) tulajdonságot kell beállítani, igaz ("defaultSslBinding": "true"). Ha az ügyfél nem állítja be a tulajdonságot, az alapértelmezett tanúsítvány alapértelmezett Proxy tartomány a(z) *.azure-api.net kiadott tanúsítványt.

## <a name="support-for-putpost-request-with-large-payload"></a>A nagy méretű tartalom PUT/POST kérelem támogatása

APIM proxykiszolgáló támogatja a nagy méretű tartalom kérelem ügyféloldali tanúsítványok használatával a HTTPS (például hasznos > 40 KB). Ha szeretné megakadályozni a kiszolgálói kérelem zárolása, az ügyfelek állíthatja be a tulajdonság ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/CreateOrUpdate#hostnameconfiguration) a Proxy állomásnevet. Ha a tulajdonság értéke igaz, az ügyfél tanúsítvány kérelmezése időpontban SSL/TLS kapcsolat, a HTTP-kérelem exchange előtt. A beállítás vonatkozik mivel a **Proxy állomásnév** szint összes kapcsolatkérelmeknek kérje meg az ügyféltanúsítványt. Az ügyfelek legfeljebb 20 egyéni tartományok konfigurálása a Proxy (csak akkor támogatott a a **prémium** réteg) és a korlátozás.

