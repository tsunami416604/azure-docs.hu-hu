<properties 
    pageTitle="疑難排解：Azure AD 密碼管理 |Microsoft Azure" 
    description="Azure AD 密碼管理的常見疑難排解步驟，包括重設、變更、回寫、註冊，以及尋求協助時應包含的資訊。" 
    services="active-directory" 
    documentationCenter="" 
    authors="asteen" 
    manager="kbrint" 
    editor="billmath"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="asteen"/>

# 如何疑難排解密碼管理
如果您有密碼管理方面的問題，我們在此提供協助。 您可能會碰到的大多數問題都可以透過以下幾個簡單的疑難排解步驟來加以解決，請閱讀其內容來疑難排解您的部署：

* [**您需要協助時應包含的資訊**](#information-to-include-when-you-need-help)
* [**使用 Azure 管理入口網站中的密碼管理組態問題**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Azure 管理入口網站中的密碼管理報告問題**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**重設註冊入口網站的密碼問題**](#troubleshoot-the-password-reset-registration-portal)
* [**重設入口網站的密碼問題**](#troubleshoot-the-password-reset-portal)
* [**密碼回寫的問題**](#troubleshoot-password-writeback)
  - [密碼回寫事件記錄檔錯誤碼](#password-writeback-event-log-error-codes)
  - [密碼回寫連線的問題](#troubleshoot-password-writeback-connectivity)

如果您試過下列疑難排解步驟，但仍遇到問題，您可以將問題張貼在 [Azure AD 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) 或連絡支援人員，我們將了解您的問題，我們可以。

## 您需要協助時應包含的資訊

如果在進行過下列指導方針後仍無法解決問題，請連絡我們的支援工程師。 當您與他們連絡時，建議您包含下列資訊：

 - **錯誤的一般描述** – 什麼確切的錯誤訊息的使用者看到？  如果沒有任何錯誤訊息，請詳細說明您所注意到的未預期行為。
 - **頁面** – 哪一頁上看到此錯誤是你 （包含 URL）？
 - **日期 / 時間 / 時區** – 時的精確日期和時間看到錯誤 （包含時區）？
 - **支援的程式碼** – 使用者看到錯誤 （若要尋找這、 重現錯誤，然後按一下畫面底部的支援程式碼連結及所產生的 GUID 傳送給支援工程師） 時，產生的支援程式碼是什麼。 
   - 如果您所在的頁面底部沒有支援碼，請按 F12，搜尋 SID 和 CID，然後將這兩個結果傳送給支援工程師。

    ![][001]

 - **使用者識別碼** – 看到錯誤 (例如 user@contoso.com) 的使用者識別碼是什麼？
 - **使用者的相關資訊** – 使用者已同盟、 密碼雜湊同步處理、 僅限雲端？  使用者是否已獲得 AAD Premium 或 AAD Basic 授權？
 - **應用程式事件記錄檔** – 如果您使用密碼回寫，而且錯誤位於您內部部署基礎結構，請從 Azure AD Connect 伺服器應用程式事件記錄檔的複本進行壓縮，然後連同要求一起送。

包含這些資訊將有助於我們儘快為您解決問題。


## 疑難排解 Azure 管理入口網站中的密碼重設設定
如果您在設定密碼重設時遇到錯誤，您可以遵循下列疑難排解步驟來加以解決：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者會看到哪些錯誤？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我沒看到 <strong>使用者密碼重設原則 </strong>區段底下 <strong>設定</strong> 在 Azure 管理入口網站] 索引標籤</p>
            </td>
            <td>
              <p> <strong>使用者密碼重設原則 </strong>看不到區段 <strong>設定</strong> 在 Azure 管理入口網站] 索引標籤。</p>
            </td>
            <td>
              <p>如果沒有指派 AAD Premium 或 AAD Basic 授權給執行此作業的系統管理員，就會發生這種情況。 </p>
              <p>若要修正此問題，AAD Premium 或 AAD Basic 授權指派給問題的系統管理員帳戶瀏覽至 <strong>授權</strong> 索引標籤，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我沒看到任何組態選項底下 <strong>使用者密碼重設原則</strong> 一節所述的文件。</p>
            </td>
            <td>
              <p> <strong>使用者密碼重設原則 </strong>區段可見，但其下方出現的唯一旗標是 <strong>使用者已啟用密碼重設</strong> 旗標。</p>
            </td>
            <td>
              <p>當您切換時，就會出現其餘 UI <strong>使用者已啟用密碼重設</strong> 旗標設為 <strong>[是]。</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我沒看到特定設定選項。</p>
            </td>
            <td>
              <p>比方說，我看不見 <strong>的使用者必須確認其連絡資料之前的天數</strong> 選項出現在我捲動 <strong>使用者密碼重設原則</strong> 區段 （或相同問題的其他範例）。</p>
            </td>
            <td>
              <p>許多 UI 項目只會在需要時出現。 如果您想要看到這些項目，請嘗試啟用頁面上的所有選項。</p>
              <p>請參閱 <a href="../active-directory-passwords-customize#password-management-behavior">自訂使用者密碼重設原則</a> 如需所有可用控制項的詳細資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我沒看到 <strong>密碼回寫至內部部署上</strong> 組態選項</p>
            </td>
            <td>
              <p> <strong>密碼回寫至內部部署上</strong> 選項下沒有出現 <strong>設定</strong> 在 Azure 管理入口網站] 索引標籤</p>
            </td>
            <td>
              <p>在您下載 Azure AD Connect 並設定密碼回寫後，才會出現此選項。 當您完成上述動作時，此選項便會出現，並可讓您啟用或停用雲端回寫。</p>
              <p>請參閱 <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">如何啟用/停用密碼回寫</a> 如需有關如何執行這項操作。</p>
            </td>
          </tr>
        </tbody></table>

## 疑難排解 Azure 管理入口網站中的密碼管理報告
如果您在使用密碼管理報告時遇到錯誤，您可以遵循下列疑難排解步驟來加以解決：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者會看到哪些錯誤？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>我沒看到任何密碼管理報表</p>
            </td>
            <td>
              <p> <strong>密碼重設活動</strong> 和 <strong>密碼重設註冊活動</strong> 報告底下沒有出現 <strong>活動記錄</strong> 中的報表 <strong>報表</strong> ] 索引標籤。</p>
            </td>
            <td>
              <p>如果沒有指派 AAD Premium 或 AAD Basic 授權給執行此作業的系統管理員，就會發生這種情況。 </p>
              <p>若要修正此問題，AAD Premium 或 AAD Basic 授權指派給問題的系統管理員帳戶瀏覽至 <strong>授權</strong> 索引標籤，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者註冊顯示多個時間</p>
            </td>
            <td>
              <p>當使用者註冊備用電子郵件、行動電話和安全性問題時，這些活動會各自顯示一行，而非全部顯示在同一行。</p>
            </td>
            <td>
              <p>目前，當使用者註冊時，我們無法假設他們會在註冊頁面上註冊出現的所有項目。 因此，我們目前會將所註冊的每一筆個別資料記錄為個別事件。</p>
              <p>如果您想要彙總此資料，您可以下載報告，並在 Excel 樞紐分析表中開啟資料，以獲得更大的編輯彈性。</p>
            </td>
          </tr>
        </tbody></table>

## 疑難排解密碼重設註冊入口網站
如果您在註冊使用者的密碼重設功能時遇到錯誤，您可以遵循下列疑難排解步驟來加以解決：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者會看到哪些錯誤？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>目錄未啟用密碼重設功能</p>
            </td>
            <td>
              <p>您的系統管理員還沒為您啟用這項功能。</p>
            </td>
            <td>
              <p>交換器 <strong>使用者已啟用密碼重設</strong> 旗標設為 <strong>是</strong> 文字，然後按 <strong>儲存</strong> Azure 管理入口網站目錄設定] 索引標籤中。 您必須指派 Azure AD Premium 或 Basic 授權給執行此作業的系統管理員。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>使用者尚未獲得 AAD Premium 或 AAD Basic 授權</p>
            </td>
            <td>
              <p>您的系統管理員還沒為您啟用這項功能。</p>
            </td>
            <td>
              <p>將 Azure AD Premium 或 Azure AD Basic 授權指派給使用者在 <strong>授權</strong> 在 Azure 管理入口網站] 索引標籤。 您必須指派 Azure AD Premium 或 Basic 授權給執行此作業的系統管理員。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>處理要求時發生錯誤</p>
            </td>
            <td>
              <p>使用者看到錯誤指出：</p>
              <p>
                
              </p>
              <p>Error processing request </p>
              <p>When attempting to reset a password.</p>
            </td>
            <td>
              <p>This can be caused by many issues, but generally this error is caused by either a service outage or configuration issue that cannot be resolved. </p>
              <p>If you see this error and it is impacting your business, please contact support and we will assist you ASAP. See <a href="#information-to-include-when-you-need-help">Information to include when you need help</a> to see what you should provide to the support engineer to aid in a speedy resolution.</p>
            </td>
          </tr>
        </tbody></table>

## 疑難排解密碼重設入口網站
如果您在重設使用者的密碼時遇到錯誤，您可以遵循下列疑難排解步驟來加以解決：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者會看到哪些錯誤？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>目錄未啟用密碼重設功能</p>
            </td>
            <td>
              <p>您的帳戶未啟用密碼重設功能</p>
              <p>很抱歉，您的系統管理員還沒將您的帳戶設定用於此服務。 </p>
              <p>
                
              </p>
              <p>If you'd like, we can contact an administrator in your organization to reset your password for you.</p>
            </td>
            <td>
              <p>Switch the <strong>Users Enabled for Password Reset</strong> flag to <strong>Yes</strong> and hit <strong>Save</strong> in the Azure Management Portal directory configuration tab. You must have an Azure AD Premium or Basic License assigned to the admin performing this operation.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>User does not have an AAD Premium or AAD Basic license assigned</p>
            </td>
            <td>
              <p>While we cannot reset non-admin account passwords automatically, we can contact your organization's admin to do it for you.</p>
            </td>
            <td>
              <p>Assign an Azure AD Premium or Azure AD Basic license to the user under the <strong>Licenses</strong> tab in the Azure Management Portal. You must have an Azure AD Premium or Basic License assigned to the admin performing this operation.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory is enabled for password reset, but user has missing or mal-formed authentication information</p>
            </td>
            <td>
              <p>Your account is not enabled for password reset</p>
              <p>We're sorry, but your administrator has not set up your account for use with this service. </p>
              <p>
                
              </p>
              <p>If you'd like, we can contact an administrator in your organization to reset your password for you.</p>
            </td>
            <td>
              <p>Ensure that user has properly formed contact data on file in the directory before proceeding. See <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">What data is used by password reset</a> for information on how to configure authentication information in the directory so that users do not see this error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory is enabled for password reset, but a user only has one piece of contact data on file when policy is set to require two verification steps</p>
            </td>
            <td>
              <p>Your account is not enabled for password reset</p>
              <p>We're sorry, but your administrator has not set up your account for use with this service. </p>
              <p>
                
              </p>
              <p>If you'd like, we can contact an administrator in your organization to reset your password for you.</p>
            </td>
            <td>
              <p>Ensure that user has at least two properly configured contact methods (e.g., both Mobile Phone and Office Phone) before proceeding. See <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">What data is used by password reset</a> for information on how to configure authentication information in the directory so that users do not see this error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory is enabled for password reset, and user is properly configured, but user is unable to be contacted </p>
            </td>
            <td>
              <p>Oops!  We encountered an unexpected error while contacting you.</p>
            </td>
            <td>
              <p>This could be the result of a temporary service error or misconfigured contact data that we could not properly detect. If the user waits 10 seconds, a try again and “contact your administrator” link appears. Clicking try again will re-dispatch the call, whereas clicking “contact your administrator” will send a form email to user, password, or global admins (in that precedence order) requesting a password reset to be performed for that user account.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>User never receives the password reset SMS or phone call</p>
            </td>
            <td>
              <p>User clicks “text me” or “call me” and then never receives anything.</p>
            </td>
            <td>
              <p>This could be the result of a mal-formed phone number in the directory. Make sure the phone number is in the format “+ccc xxxyyyzzzzXeeee”. To learn more about formatting phone numbers for use with password reset see <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">What data is used by password reset</a>.</p>
              <p>If you require an extension to be routed to the user in question, note that password reset does not support extensions, even if you specify one in the directory (they are stripped before the call is dispatched). Try using a number without an extension, or integrating the extension into the phone number in your PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>User never receives password reset email</p>
            </td>
            <td>
              <p>User clicks “email me” and then never receives anything.</p>
            </td>
            <td>
              <p>The most common cause for this issue is that the message is rejected by a spam filter. Check your spam, junk, or deleted items folder for the email.</p>
              <p>Also ensure that you are checking the right email for the message…lots of people have very similar email addresses and end up checking the wrong inbox for the message. If neither of these options work, it’s also possible that the email address in the directory is malformed, check to make sure the email address is the right one and try again. To learn more about formatting email addresses for use with password reset see <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">What data is used by password reset</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>I have set a password reset policy, but when an admin account uses password reset, that policy is not applied</p>
            </td>
            <td>
              <p>Admin accounts resetting their passwords see the same options enabled for password reset, email and mobile phone, no matter what policy is set under the <strong>User Password Reset Policy</strong> section of the <strong>Configure</strong> tab.</p>
            </td>
            <td>
              <p>The options configured under the <strong>User Password Reset Policy</strong> section of the <strong>Configure</strong> tab only apply to end users in your organization.</p>
              <p>Microsoft manages and controls the Admin password reset policy in order to ensure the highest level of security</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>User prevented from attempting password reset too many times in a day</p>
            </td>
            <td>
              <p>User sees an error stating:</p>
              <p>
                
              </p>
              <p>Please use another option.</p>
              <p>You've tried to verify your account too many times in the last 1 hour(s). For security reasons, you'll have to wait 24 hour(s) before you can try again. </p>
              <p>If you'd like, we can contact an administrator in your organization to reset your password for you.</p>
            </td>
            <td>
              <p>We implement an automatic throttling mechanism to block users from attempting to reset their passwords too many times in a short period of time. This occurs when:</p>
              <ol class="ordered">
                <li>
                                        User attempts to validate a phone number 5 times in one hour.<br\><br\></li>
                <li>
                                        User attempts to use the security questions gate 5 times in one hour.<br\><br\></li>
                <li>
                                        User attempts to reset a password for the same user account 5 times in one hour.<br\><br\></li>
              </ol>
              <p>To fix this, instruct the user to wait 24 hours after the last attempt, and the user will then be able to reset his or her password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>User sees an error when validating his or her phone number</p>
            </td>
            <td>
              <p>When attempting to verify a phone to use as an authentication method, the user sees an error stating:</p>
              <p>
                
              </p>
              <p>Incorrect phone number specified.</p>
            </td>
            <td>
              <p>This error occurs when the phone number entered does not match the phone number on file.</p>
              <p>Make sure the user is entering the complete phone number, including area and country code, when attempting to use a phone-based method for password reset.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error processing request</p>
            </td>
            <td>
              <p>User sees an error that states:</p>
              <p>
                
              </p>
              <p>Error processing request </p>
              <p>When attempting to reset a password.</p>
            </td>
            <td>
              <p>This can be caused by many issues, but generally this error is caused by either a service outage or configuration issue that cannot be resolved. </p>
              <p>If you see this error and it is impacting your business, please contact support and we will assist you ASAP. See <a href="#information-to-include-when-you-need-help">Information to include when you need help</a> to see what you should provide to the support engineer to aid in a speedy resolution.</p>
            </td>
          </tr>
        </tbody></table>

## 疑難排解密碼回寫
如果您在啟用、停用或使用密碼回寫時遇到錯誤，您可以遵循下列疑難排解步驟來加以解決：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>錯誤案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用者會看到哪些錯誤？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>一般的上架和啟動失敗</p>
            </td>
            <td>
              <p>內部部署未啟動密碼重設服務，Azure AD Connect 電腦的應用程式事件記錄檔中有錯誤 6800。</p>
              <p>
                
              </p>
              <p>After onboarding, federated or password hash synced users cannot reset their passwords.</p>
            </td>
            <td>
              <p>When Password Writeback is enabled, the sync engine will call the writeback library to perform the configuration (onboarding) by talking to the cloud onboarding service. Any errors encountered during onboarding or while starting the WCF endpoint for Password Writeback will result in errors in the Event log in your Azure AD Connect machine’s event log.</p>
              <p>During restart of ADSync service, if writeback was configured, the WCF endpoint will be started up. However, if the startup of the endpoint fails, we will simply log event 6800 and let the sync service startup. Presence of this event means that the Password Writeback endpoint was not started up. Event log details for this event (6800) along with event log entries generate by PasswordResetService component will indicate why the endpoint could not be started up. Review these event log errors and try to re-start the Azure AD Connect if Password Writeback still isn’t working. If the problem persists, try to disable and re-enable Password Writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error configuring writeback during Azure AD Connect installation.</p>
            </td>
            <td>
              <p>At the last step of the Azure AD Connect installation process, you see an error indicating that Password Writeback could not be configured.</p>
              <p>
                
              </p>
              <p>The Azure AD Connect Application event log contains error 32009 with text “Error getting auth token”.</p>
            </td>
            <td>
              <p>This error occurs in the following two cases:</p>
              <ul>
                <li class="unordered">
                                        You have specified an incorrect password for the global administrator account specified at the beginning of the Azure AD Connect installation process.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        You have attempted to use a federated user for the global administrator account specified at the beginning of the Azure AD Connect installation process.<br\><br\></li>
              </ul>
              <p>To fix this error, please ensure that you are not using a federated account for the global administrator you specified at the beginning of the Azure AD Connect installation process, and that the password specified is correct.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error configuring writeback during Azure AD Connect installation.</p>
            </td>
            <td>
              <p>The Azure AD Connect machine event log contains error 32002 thrown by the PasswordResetService.</p>
              <p>
                
              </p>
              <p>The error reads: “Error Connecting to ServiceBus, The token provider was unable to provide a security token…”</p>
              <p>
                
              </p>
            </td>
            <td>
              <p>The root cause of this error is that the password reset service running in your on-premises environment is not able to connect to the service bus endpoint in the cloud. This error is normally normally caused by a firewall rule blocking an outbound connection to a particular port or web address.</p>
              <p>
                
              </p>
              <p>Make sure your firewall allows outbound connections for the following:</p>
              <ul>
                <li class="unordered">
                                        All traffic over TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Outbound connections to <br\><br\></li>
              </ul>
              <p>
                
              </p>
              <p>Once you have updated these rules, reboot the Azure AD Connect machine and Password Writeback should start working again.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Password Writeback endpoint on-prem not reachable</p>
            </td>
            <td>
              <p>After working for some time, federated or password hash synced users cannot reset their passwords.</p>
            </td>
            <td>
              <p>In some rare cases, the Password Writeback service may fail to re-start when Azure AD Connect has re-started. In these cases, first, check whether Password Writeback appears to be enabled on-prem. This can be done using the Azure AD Connect wizard or powershell (See HowTos section above).If the feature appears to be enabled, try enabling or disabling the feature again either through the UI or PowerShell. See “Step 2: Enable Password Writeback on your Directory Sync computer &amp; configure firewall rules” in <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">How to enable/disable Password Writeback</a> for more information on how to do this.</p>
              <p>
                
              </p>
              <p>If this doesn’t work, try completely uninstalling and re-installing Azure AD Connect.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Permissions errors</p>
            </td>
            <td>
              <p>Federated or password hash sync’d users who attempt to reset their passwords see an error after submitting the password indicating there was a service problem.</p>
              <p>
                
              </p>
              <p>In addition to this, during password reset operations, you may see an error regarding management agent was denied access in your on premises event logs.</p>
            </td>
            <td>
              <p>If you see these errors in your event log, confirm that the AD MA account (that was specified in the wizard at the time of configuration) has the necessary permissions for Password Writeback.</p>
              <p>
                
              </p>
              <p>NOTE that once this permission is given it can take up to 1 hour for the permissions to trickle down via sdprop background task on the DC. </p>
              <p>For password reset to work, the permission needs to be stamped on the security descriptor of the user object whose password is being reset. Until this permission shows up on the user object, password reset will continue to fail with access denied.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error when configuring Password Writeback from the Azure AD Connect configuration wizard </p>
            </td>
            <td>
              <p>“Unable to Locate MA” error in Wizard while enabling/disabling Password Writeback</p>
            </td>
            <td>
              <p>There is a known bug in the released version of Azure AD Connect which manifests in the following situation:</p>
              <ol class="ordered">
                <li>
                                        You configure Azure AD Connect for tenant abc.com (Verified domain) using creds . This results in AAD connector with name “abc.com – AAD” being created.<br\><br\></li>
                <li>
                                        You then then change the AAD creds for the connector (using old sync UI) to  (note it’s the same tenant but different domain name). <br\><br\></li>
                <li>
                                        Now you try to enable/disable Password Writeback. The wizard will construct the name of the connector using the creds, as “abc.onmicrosoft.com – AAD” and pass to the Password Writeback cmdlet. This will fail because there is no connector created with this name.<br\><br\></li>
              </ol>
              <p>This has been fixed in our latest builds. If you have an older build, the one workaround is to use the powershell cmdlet to enable/disable the feature. See “Step 2: Enable Password Writeback on your Directory Sync computer &amp; configure firewall rules” in <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">How to enable/disable Password Writeback</a> for more information on how to do this.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Unable to reset password for users in special groups such as Domain Admins / Enterprise Admins etc.</p>
            </td>
            <td>
              <p>Federated or password hash sync’d users who are part of protected groups and attempt to reset their passwords see an error after submitting the password indicating there was a service problem.</p>
            </td>
            <td>
              <p>Privileged users in Active Directory are protected using AdminSDHolder. See <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> for more details. </p>
              <p>
                
              </p>
              <p>This means the security descriptors on these objects are periodically checked to match the one specified in AdminSDHolder and are reset if they are different. The additional permissions that are needed for Password Writeback therefore do not trickle to such users. This can result in Password Writeback not working for such users.As a result, we do not support managing passwords for users within these groups because it breaks the AD security model.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Reset operations fails with Object could not be found</p>
            </td>
            <td>
              <p>Federated or password hash sync’d users who attempt to reset their passwords see an error after submitting the password indicating there was a service problem.</p>
              <p>
                
              </p>
              <p>In addition to this, during password reset operations, you may see an error in your event logs from the Azure AD Connect service indicating an “Object could not be found” error.</p>
            </td>
            <td>
              <p>This error usually indicates that the sync engine is unable to find either the user object in the AAD connector space or the linked MV or AD connector space object. </p>
              <p>
                
              </p>
              <p>To troubleshoot this, make sure that the user is indeed synced from on-prem to AAD via the current instance of Azure AD Connect and inspect the state of the objects in the connector spaces and MV. Confirm that the AD CS object is connector to the MV object via the “Microsoft.InfromADUserAccountEnabled.xxx” rule.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Reset operations fails with Multiple matches found eror</p>
            </td>
            <td>
              <p>Federated or password hash sync’d users who attempt to reset their passwords see an error after submitting the password indicating there was a service problem.</p>
              <p>
                
              </p>
              <p>In addition to this, during password reset operations, you may see an error in your event logs from the Azure AD Connect service indicating a “Multiple maches found” error.</p>
            </td>
            <td>
              <p>This indicates that the sync engine detected that the MV object is connected to more than one AD CS objects via the “Microsoft.InfromADUserAccountEnabled.xxx”. This means that the user has an enabled account in more than one forest. </p>
              <p>
                
              </p>
              <p>Currently this scenario is not supported for Password Writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Password operations fail with a configuration error.</p>
            </td>
            <td>
              <p>Password operations fail with a configuration error. The application event log contains Azure AD Connect error 6329 with text: 0x8023061f (The operation failed because password synchronization is not enabled on this Management Agent.)</p>
            </td>
            <td>
              <p>This occurs if the Azure AD Connect configuration is changed to add&nbsp;a new AD forest (or to remove and re-add an existing forest) <strong>after</strong> the Password Writeback feature has already been enabled. Password operations for users in such newly added forests will fail. To fix the problem, disable and re-enable the Password Writeback feature after the forest configuration changes have been completed.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Writing back passwords that have been reset by users works properly, but writing back passwords changed by a user or reset for a user by an administrator fails.</p>
            </td>
            <td>
              <p>When attempting to reset a password on behalf of a user from the Azure Management Portal, you see a message stating: “The password reset service running in your on-premises environment does not support administrators resetting user passwords. Please upgrade to the latest version of Azure AD Connect to resolve this.”</p>
            </td>
            <td>
              <p>This occurs when the version of the synchronization engine does not support the particular Password Writeback operation that was used. Versions of Azure AD Connect later than 1.0.0419.0911 support all password management operations, including password reset writeback, password change writeback, and administrator-initiated password reset writeback from the Azure Management Portal.&nbsp; DirSync versions later than 1.0.6862 support password reset writeback only. To resolve this issue, we highly recommend that you install the latest version of Azure AD Connect or Azure Active Directory Connect (for more information, see <a href="../active-directory-aadconnect#download-azure-ad-connect">Directory Integration Tools</a>) to resolve this issue and to get the most out of Password Writeback in your organization.</p>
            </td>
          </tr>
        </tbody></table>


## 密碼回寫事件記錄檔錯誤碼
疑難排解密碼回寫問題時的最佳作法，是檢查 Azure AD Connect 電腦上的應用程式事件記錄檔。 這個事件記錄檔會包含密碼回寫的兩個相關來源的事件。 PasswordResetService 來源會說明與密碼回寫作業相關的作業和問題。 ADSync 來源會說明與 AD 環境中的密碼設定相關的作業和問題。

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>程式碼</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>名稱 / 訊息</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>來源</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>描述</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>BAIL: MMS(4924) 0x80230619 – “有一項限制讓密碼無法變更為目前指定的密碼。”</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>當密碼回寫服務嘗試在本機目錄上設定不符合密碼使用期限、歷程記錄、複雜度或網域篩選需求的密碼時，就會發生此錯誤。</p>
              <ul>
                <li class="unordered">
                                        If you have a minimum password age, and have recently changed the password within that window of time, you will not be able to change the password again until it reaches the specified age in your domain. For testing purposes, minimum age should be set to 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password history requirements enabled, then you must select a password that has not been used in the last N times, where N is the password history setting. If you do select a password that has been used in the last N times, then you will see a failure in this case. For testing purposes, history should be set to 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password complexity requirements, all of them will be enforced when the user attempts to change or reset a password.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password filters enabled, and a user selects a password which does not meet the filtering criteria, then the reset or change operation will fail.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>HR 8023042</p>
            </td>
            <td>
              <p>同步處理引擎傳回的錯誤 hr= 80230402，訊息=嘗試取得物件失敗，因為存在具有相同錨點的重複項目</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>在多個網域中啟用相同的使用者識別碼時會發生此事件。  例如，如果您在同步處理的帳戶/資源樹系中皆存在相同的使用者識別碼且皆為啟用時，可能會發生此錯誤。  </p>
              <p>如果您使用非唯一的錨點屬性 (例如別名或 UPN)，而且兩個使用者共用該相同的錨點屬性時，也會發生此錯誤。</p>
              <p>若要解決這個問題，請確定您的網域內沒有重複的使用者，以及每個使用者皆使用唯一的錨點屬性。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示內部部署服務偵測到源自雲端、針對已同盟或密碼雜湊同步處理的使用者所提出的密碼重設要求。 這個事件是每個密碼重設回寫作業的第一個事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示使用者在密碼重設作業期間選取了新的密碼，我們判斷此密碼符合公司的密碼需求，因此該密碼已成功回寫至本機 AD 環境。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示使用者選取了密碼，該密碼已成功抵達內部部署環境，但是當我們嘗試在本機 AD 環境中設定密碼時，發生了失敗狀況。 此狀況有幾個可能原因：</p>
              <ul>
                <li class="unordered">
                                        The user’s password does not meet the age, history, complexity, or filter requirements for the domain. Try a completely new password to resolve this.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The MA service account does not have the appropriate permissions to set the new password on the user account in question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The user’s account is in a protected group, such as domain or enterprise admins, which disallows password set operations.<br\><br\></li>
              </ul>
              <p>請參閱 <a href="#troubleshoot-password-writeback">疑難排解密碼回寫</a> 若要深入了解有哪些其他狀況會造成這個錯誤。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件發生在您對 Azure AD Connect 啟用密碼回寫時，並且表示我們已開始將您的組織上架到密碼回寫 Web 服務。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示上架程序已順利完成，且密碼回寫功能已準備好可供使用。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示內部部署服務偵測到源自雲端、針對已同盟或密碼雜湊同步處理的使用者所提出的密碼變更要求。 這個事件是每個密碼變更回寫作業的第一個事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示使用者在密碼變更作業期間選取了新的密碼，我們判斷此密碼符合公司的密碼需求，因此該密碼已成功回寫至本機 AD 環境。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示使用者選取了密碼，該密碼已成功抵達內部部署環境，但是當我們嘗試在本機 AD 環境中設定密碼時，發生了失敗狀況。 此狀況有幾個可能原因：</p>
              <ul>
                <li class="unordered">
                                        The user’s password does not meet the age, history, complexity, or filter requirements for the domain. Try a completely new password to resolve this.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The MA service account does not have the appropriate permissions to set the new password on the user account in question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The user’s account is in a protected group, such as domain or enterprise admins, which disallows password set operations.<br\><br\></li>
              </ul>
              <p>請參閱 <a href="#troubleshoot-password-writeback">疑難排解密碼回寫</a> 若要深入了解有哪些其他狀況會造成此錯誤。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>內部部署服務偵測到源自系統管理員代表使用者針對已同盟或密碼雜湊同步處理的使用者所提出的密碼重設要求。 這個事件是每個系統管理員所起始的密碼重設回寫作業的第一個事件。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>系統管理員在系統管理員所起始的密碼重設作業期間選取了新的密碼，我們判斷此密碼符合公司的密碼需求，因此該密碼已成功回寫至本機 AD 環境。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>系統管理員代表使用者選取了密碼，該密碼已成功抵達內部部署環境，但是當我們嘗試在本機 AD 環境中設定密碼時，發生了失敗狀況。 此狀況有幾個可能原因：</p>
              <ul>
                <li class="unordered">
                                        The user’s password does not meet the age, history, complexity, or filter requirements for the domain. Try a completely new password to resolve this.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The MA service account does not have the appropriate permissions to set the new password on the user account in question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The user’s account is in a protected group, such as domain or enterprise admins, which disallows password set operations.<br\><br\></li>
              </ul>
              <p>請參閱 <a href="#troubleshoot-password-writeback">疑難排解密碼回寫</a> 若要深入了解有哪些其他狀況會造成這個錯誤。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件發生在您對 Azure AD Connect 停用密碼回寫時，並且表示我們已開始將您的組織下架到密碼回寫 Web 服務。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示下架程序已順利完成，且密碼回寫功能已順利停用。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示下架程序未成功。 原因可能是雲端或設定期間指定之內部部署系統管理員帳戶的權限發生錯誤，或您嘗試在停用密碼回寫時使用已同盟的雲端全域系統管理員。 若要修正此問題，請檢查您的系統管理權限，並確認您沒有在設定密碼回寫功能時使用任何已同盟的帳戶。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示密碼回寫服務已成功啟動，並已準備好接受來自雲端的密碼管理要求。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示密碼回寫服務已停止，且來自雲端的所有密碼管理要求都不會成功。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們已順利擷取 Azure AD Connect 設定期間所指定之全域系統管理員的授權權杖，以便開始下架或上架程序。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們已成功建立密碼加密金鑰，此金鑰將會用來加密從雲端傳送至內部部署環境的密碼。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示密碼管理作業期間發生未知的錯誤。 請查看事件中的例外狀況文字，以獲得詳細資訊。 如果您遇到問題，請嘗試停用再重新啟用密碼回寫。 如果這麼做沒有用，請將事件記錄檔複本連同內部指定的追蹤識別碼交給支援工程師。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>此事件表示連線到雲端密碼重設服務時發生錯誤，且此事件通常發生在內部部署服務無法連線到密碼重設 Web 服務時。 </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示連線到租用戶的服務匯流排執行個體時發生錯誤。 可能原因是您在內部部署環境中封鎖了輸出連線。 請檢查您的防火牆，確保您允許連線透過 TCP 443 和 <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, ，然後再試一次。 如果仍遇到問題，請嘗試停用再重新啟用密碼回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示傳遞到我們的 Web 服務 API 的輸入無效。 請重試一次此作業。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示在解密從雲端抵達的密碼時發生錯誤。 原因可能是雲端服務和內部部署環境的解密金鑰不符。 若要解決此問題，請停用再重新啟用內部部署環境的密碼回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>上架期間，我們將租用戶特定的資訊儲存在內部部署環境的設定檔中。 這個事件表示儲存此檔案時發生錯誤，或啟動服務時發生檔案讀取錯誤。 若要修正此問題，請嘗試停用再重新啟用密碼回寫，強制重新寫入此設定檔。 </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATED – Azure AD Connect 不會出現此事件，只有極為早期的 DirSync 組建支援回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>上架期間，我們會從雲端傳送資料到內部部署密碼重設服務。 該資料接著會寫入至記憶體內檔案，然後再傳送至同步處理服務，以將此資訊安全地儲存在磁碟上。 這個事件表示在記憶體中寫入或更新該資料時發生問題。 若要修正此問題，請嘗試停用再重新啟用密碼回寫，強制重新寫入此設定。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們從密碼重設 Web 服務所收到的回應無效。 若要修正此問題，請嘗試停用再重新啟用密碼回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們無法取得 Azure AD Connect 設定期間所指定之全域系統管理員帳戶的授權權杖。 造成這個錯誤的原因可能是全域系統管理員帳戶所指定的使用者名稱或密碼錯誤，或是指定的全域系統管理員帳戶已同盟。 若要修正此問題，請以正確的使用者名稱和密碼重新執行設定，並確保系統管理員是受管理的 (僅限雲端或已密碼同步處理的) 帳戶。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示在產生密碼加密金鑰或解密從雲端服務抵達的密碼時發生錯誤。 此錯誤可能表示您的環境有問題。 請查看事件記錄檔的詳細資料，深入了解此問題並加以解決。 您也可以嘗試停用再重新啟用密碼回寫服務來解決這個問題。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示內部部署服務無法與密碼重設 Web 服務正確地通訊，來起始上架程序。 這可能是因為有防火牆規則或是取得租用戶的授權權杖時發生問題。 若要修正此問題，請確定您不封鎖輸出連線透過 TCP 443 和 TCP 9350-9354 或是 <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, 、 和的 AAD 系統管理員帳戶您用來將產品上架並未同盟。 </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATED – Azure AD Connect 不會出現此事件，只有極為早期的 DirSync 組建支援回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示內部部署服務無法與密碼重設 Web 服務正確地通訊，來起始下架程序。 這可能是因為有防火牆規則或是取得租用戶的授權權杖時發生問題。 若要修正此問題，請確定沒有封鎖輸出連線透過 443 或是 <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, ，和您用來下架的 AAD 系統管理員帳戶並未同盟。 </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們必須重試連線到租用戶的服務匯流排執行個體。 在正常情況下，您不必在意此事件，但如果您多次看到此事件，請考慮檢查您的服務匯流排網路連線，尤其是如果它是高延遲或低頻寬連線的話。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>為了監視密碼回寫服務的健全狀況，我們每隔 5 分鐘就會傳送活動訊號資料給我們的密碼重設 Web 服務。 這個事件表示在將此健全狀況資訊傳回到雲端 Web 服務時發生錯誤。 此健全狀況資訊不包含 OII 或 PII 資料，純綷只有活動訊號和基本服務統計資料，以便我們可以在雲端中提供服務狀態資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示 AD 傳回未知的錯誤，請檢查 Azure AD Connect 伺服器事件記錄檔中來自 ADSync 來源的事件，以獲得關於此錯誤的詳細資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示在內部部署目錄中找不到嘗試重設或變更密碼的使用者。 當使用者已自內部部署中刪除卻不在雲端中，或如果同步處理發生問題，就可能發生這個事件。 請檢查您的同步處理記錄檔，以及最後幾次執行的同步處理詳細資料，以獲得詳細資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>當密碼重設或變更要求源自雲端時，我們會使用 Azure AD Connect 設定程序期間所指定的雲端錨點，來判斷如何將該要求往回連結到內部部署環境中的使用者。 這個事件表示我們發現內部部署目錄中有兩位使用者具有相同的雲端錨點屬性。 請檢查您的同步處理記錄檔，以及最後幾次執行的同步處理詳細資料，以獲得詳細資訊。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示管理代理程式服務帳戶沒有適當的指定帳戶權限，因此無法設定新密碼。 請確定使用者樹系中的 MA 帳戶有樹系中所有物件的重設和變更密碼權限。  如需有關如何執行作業，請參閱 <a href="../active-directory-passwords-getting-started#step-4-set-up-the-appropriate-active-directory-permissions">步驟 4 ︰ 設定適當的 Active Directory 權限</a>。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們嘗試對內部部署中已停用的帳戶重設或變更密碼。 請啟用帳戶，然後再試一次此作業。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示我們嘗試對內部部署中已鎖定的帳戶重設或變更密碼。 當使用者在短時間內嘗試進行變更或重設密碼作業太多次，就可能發生鎖定。 請解除鎖定帳戶，然後再試一次此作業。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示使用者在執行密碼變更作業時，指定的目前密碼不正確。 請指定正確的目前密碼，然後再試一次。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>當密碼回寫服務嘗試在本機目錄上設定不符合密碼使用期限、歷程記錄、複雜度或網域篩選需求的密碼時，就會發生此錯誤。</p>
              <ul>
                <li class="unordered">
                                        If you have a minimum password age, and have recently changed the password within that window of time, you will not be able to change the password again until it reaches the specified age in your domain. For testing purposes, minimum age should be set to 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password history requirements enabled, then you must select a password that has not been used in the last N times, where N is the password history setting. If you do select a password that has been used in the last N times, then you will see a failure in this case. For testing purposes, history should be set to 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password complexity requirements, all of them will be enforced when the user attempts to change or reset a password.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password filters enabled, and a user selects a password which does not meet the filtering criteria, then the reset or change operation will fail.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>這個事件表示 Active Directory 的設定有問題，因此在將密碼回寫到內部部署目錄時發生問題。 請檢查 Azure AD Connect 電腦的應用程式事件記錄檔中來自 ADSync 服務的訊息，以獲得所發生錯誤的詳細資訊。 </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATED – Azure AD Connect 不會出現此事件，只有極為早期的 DirSync 組建支援回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATED – Azure AD Connect 不會出現此事件，只有極為早期的 DirSync 組建支援回寫。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DEPRECATED – Azure AD Connect 不會出現此事件，只有極為早期的 DirSync 組建支援回寫。</p>
            </td>
          </tr>
        </tbody></table>
        
## 疑難排解密碼回寫連線

如果 Azure AD Connect 的密碼回寫元件發生服務中斷，以下是可供用來解決此問題的一些快速步驟：

 - [重新啟動 Azure AD Connect 同步處理服務](#restart-the-azure-AD-sync-service)
 - [停用再重新啟用密碼回寫功能](#disable-and-re-enable-the-password-writeback-feature)
 - [安裝最新版的 Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [疑難排解密碼回寫](#troubleshoot-password-writeback)

一般而言，我們會建議您依上述順序執行這些步驟，以便以最快速的方式復原服務。

### 重新啟動 Azure AD Connect 同步處理服務
重新啟動 Azure AD Connect 同步處理服務有助於解決服務的連線問題或其他暫時性問題。

 1. 身為管理員，按一下 [ **啟動** 執行的伺服器上 **Azure AD Connect**。
 2. 型別 **"services.msc"** 在搜尋] 方塊，然後按 **Enter**。
 3. 尋找 **Microsoft Azure AD Connect** 項目。
 4. 以滑鼠右鍵按一下服務項目中，按一下 **重新啟動**, ，並等候作業完成。

    ![][002]

這些步驟將會重新建立您與雲端服務的連線，解決您可能會遇到的任何中斷問題。  如果重新啟動同步處理服務無法解決您的問題，建議您接下來試著停用再重新啟用密碼回寫功能。

### 停用再重新啟用密碼回寫功能
停用再重新啟用密碼回寫功能有助於解決連線問題。

 1. 身為管理員，開啟 **Azure AD Connect 設定精靈**。
 2. 在 **連線到 Azure AD** ] 對話方塊中，輸入您 **Azure AD 全域系統管理員認證**
 3. 在 **連線到 AD DS** ] 對話方塊中，輸入您 **AD 網域服務系統管理員認證**。
 4. 在 **用來唯一識別您的使用者** ] 對話方塊中，按一下 [ **下一步** ] 按鈕。
 5. 在 **選用功能** ] 對話方塊中，取消核取 **密碼回寫** 核取方塊。

    ![][003]

 6. 按一下 [ **下一步** 透過其餘的對話方塊頁面，而不需要變更任何項目，直到您進入 **準備好設定** 頁面。
 7. 請確認設定頁面顯示 **密碼回寫選項為停用** ，然後按一下綠色 **設定** ] 按鈕認可變更。
 8. 在 **已經完成** ] 對話方塊中，取消選取 **立即同步處理** 選項，然後再按一下 **完成** 關閉精靈。
 9. 重新開啟 **Azure AD Connect 設定精靈**。
 10.    **重複步驟 2-8**, ，但請確定您 **勾選密碼回寫選項** 上 **選用功能** 畫面來重新啟用服務。

    ![][004]

這些步驟將會重新建立您與雲端服務的連線，解決您可能會遇到的任何中斷問題。 

如果停用再重新啟用密碼回寫功能無法解決您的問題，建議您接下來試著重新安裝 Azure AD Connect。

### 安裝最新版的 Azure AD Connect
重新安裝 Azure AD Connect 封裝將可解決任何可能會影響您能否連線到雲端服務或能否管理本機 AD 環境中密碼的設定問題。 
建議您只在嘗試過上述前兩個步驟後，才執行此步驟。

 1. 下載最新版的 Azure AD Connect [這裡](active-directory-aadconnect.md#download-azure-ad-connect)。
 2. 由於您已安裝 Azure AD Connect，您只需要執行就地升級，即可將 Azure AD Connect 安裝更新為最新版。
 3. 執行下載的封裝，並遵循螢幕上的指示來更新您的 Azure AD Connect 電腦。  除非您已自訂的方塊同步處理規則外，不需要任何額外的手動步驟，在此情況下您應該會 **進行升級之前，先將這些備份並手動重新部署這些在您完成後**。

這些步驟將會重新建立您與雲端服務的連線，解決您可能會遇到的任何中斷問題。 

如果安裝最新版的 Azure AD Connect 伺服器無法解決您的問題，建議您最後在安裝最新版的同補處理 QFE 後試著停用再重新啟用密碼回寫。 

如果這麼做無法解決您的問題，則我們建議您看看 [疑難排解密碼回寫](#troubleshoot-password-writeback) 和 [Azure AD 密碼管理常見問題集](active-directory-passwords-faq.md) 查看如果您的問題可能會那里討論到。


<br/>
<br/>
<br/>

## 密碼重設文件的連結
以下是所有 Azure AD 密碼重設文件頁面的連結： 

* [**您自己的密碼重設**](active-directory-passwords-update-your-own-password.md) -深入了解如何重設或變更您自己的密碼為系統的使用者
* [**它的運作方式**](active-directory-passwords-how-it-works.md) -了解六個不同元件服務，以及每個未
* [**開始使用**](active-directory-passwords-getting-started.md) -了解如何讓使用者重設及變更雲端或內部部署密碼
* [**自訂**](active-directory-passwords-customize.md) -了解如何自訂外觀和行為的服務，貴組織的需求
* [**最佳作法**](active-directory-passwords-best-practices.md) -了解如何快速部署且有效管理組織中的密碼
* [**深入**](active-directory-passwords-get-insights.md) -了解整合式報告功能
* [**常見問題集**](active-directory-passwords-faq.md) -取得常見問題的解答
* [**了解詳細**](active-directory-passwords-learn-more.md) -深入的技術詳細資料的服務的運作方式



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"



