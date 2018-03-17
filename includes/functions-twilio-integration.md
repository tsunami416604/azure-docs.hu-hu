Ez a minta használata szükséges a [Twilio](https://www.twilio.com/) SMS küldése mobiltelefonra szolgáltatást. Az Azure Functions már Twilio keresztül támogatása a [Twilio-kötés](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), és a mintát használja ezt a szolgáltatást.

Kell elsőként az egy Twilio-fiók. Létrehozhat egy ingyenes, https://www.twilio.com/try-twilio. Miután egy fiókot, adja hozzá a következő három **Alkalmazásbeállítások** függvény alkalmazása.

| Alkalmazás-beállítás neve | Érték Leírás |
| - | - |
| **TwilioAccountSid**  | A SID Twilio-fiókja |
| **TwilioAuthToken**   | A hitelesítési jogkivonat Twilio-fiókja |
| **TwilioPhoneNumber** | A Twilio-fiókjához társított telefonszám. SMS küldése szolgál. |