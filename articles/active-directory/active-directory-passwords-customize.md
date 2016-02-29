<properties 
    pageTitle="自訂：Azure AD 密碼管理 |Microsoft Azure" 
    description="如何自訂 Azure AD 中的密碼管理外觀、行為和通知，以符合您的需求。" 
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

# 自訂密碼管理以符合您的組織的需求
為了給予您的使用者最佳的體驗，我們建議您探索和試驗您可以使用的所有密碼管理組態選項。 事實上，您可以開始探索立即移至 [設定] 索引標籤的 **Active Directory 延伸模組** 中 [Azure 管理入口網站](https://manage.windowsazure.com)。 本主題將逐步引導您了解所有您可以從系統管理員身分進行的不同密碼管理自訂 **設定** ] 索引標籤中目錄的 [Azure 管理入口網站](https://manage.windowsazure.com), ，包括:

| 主題 |  |
| --------- | --------- |
| 如何啟用或停用密碼重設？ | [設定：啟用使用者的密碼重設](#users-enabled-for-password-reset) |
| 如何將密碼重設的範圍限定為一組特定的使用者？ | [將密碼重設限定於特定使用者](#restrict-access-to-password-reset) |
| 如何變更支援的驗證方法？ | [設定：使用者可用的驗證方法](#authentication-methods-available-to-users) |
| 如何變更必要驗證方法數目？ | [設定：必要驗證方法數目](#number-of-authentication-methods-required) |
| 如何設定自訂安全性問題？ | [設定：自訂安全性問題](#custom-security-questions) |
| 如何設定預先定義的當地語系化安全性問題？ | [設定：以知識為基礎的安全性問題](#knowledge-based-security-questions) |
| 如何變更必要的安全性問題數目？ | [設定：註冊或重設的安全性問題數目](#number-of-questions-required-to-register) |
| 如何促使我的使用者在登入時註冊？ | [以強制註冊的方式推動密碼重設](#require-users-to-register-when-signing-in) |
| 如何促使我的使用者定期重新確認他們的註冊？ | [設定：幾天後使用者必須重新確認其驗證資料](#number-of-days-before-users-must-confirm-their-contact-data) |
| 如何自訂使用者聯繫系統管理員的方式？ | [設定：自訂「連絡您的系統管理員」連結](#customize-the-contact-your-administrator-link) |
| 如何讓使用者直接解除鎖定 AD 帳戶而不必重設密碼？ | [設定：讓使用者直接解除鎖定 AD 帳戶而不必重設密碼](#allow-users-to-unlock-accounts-without-resetting-their-password) |
| 如何為使用者啟用密碼重設通知？ | [設定：在使用者的密碼重設時通知使用者](#notify-users-and-admins-when-their-own-password-has-been-reset) |
| 如何為系統管理員啟用密碼重設通知？ | [設定：在系統管理員重設其密碼時通知其他系統管理員](#notify-admins-when-other-admins-reset-their-own-passwords) |
| 如何自訂密碼重設的外觀及操作？ | [設定：公司名稱、商標和標誌 ](#password-managment-look-and-feel) |


## 密碼管理外觀
下表描述每個控制項如何影響使用者註冊密碼重設及重設其密碼的體驗。  您可以設定這些選項在 **目錄內容** 您的目錄區段 **設定** ] 索引標籤的 [Azure 管理入口網站](https://manage.windowsazure.com)。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>原則控制</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>說明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響？</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="directory-name">
                  <p>目錄名稱</p>
                </div>
              </td>
              <td>
                <p>決定使用者或系統管理員會在密碼重設電子郵件通訊上看到什麼組織名稱</p>
              </td>
              <td>
                <p>
                  <strong>「連絡您的系統管理員」電子郵件：</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines the from address friendly name, e.g. “Microsoft on behalf of <strong>Wingtip Toys</strong>」的作法指南。<br><br></li>
                  <li class="unordered">
                                                Determines the subject name of the email, e.g. “<strong>Wingtip Toys</strong> 帳戶電子郵件驗證碼 」<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設電子郵件：</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines the from address friendly name, e.g. “Microsoft on behalf of <strong>Wingtip Toys</strong>」的作法指南。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="sign-in-and-access-panel-page-appearance">
                  <p>登入和存取面板頁面外觀</p>
                </div>
              </td>
              <td>
                <p>決定造訪密碼重設頁面的使用者是否會看到 Microsoft 標誌或您自己的自訂標誌。  這個組態項目也會將您的商標加入至存取面板和登入頁面。</p>
                <p>
                  
                </p>
                <p>You can learn more about the tenant branding and customization feature at <a href="https://technet.microsoft.com/library/dn532270.aspx">Add company branding to your Sign In and Access Panel pages</a>.</p>
              </td>
              <td>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines whether or not your logo is shown at the top of the password reset portal instead of the default Microsoft logo.<br><br></li>
                  <li class="unordered">
                    <strong>Note:</strong> you may not see your logo on the first page of the password reset portal if you come to the password reset page directly.  Once a user enters his or her userID and clicks next, your logo will appear.  You can force your logo to appear on page load by passing the whr parameter to the password reset page, like this: <a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a><br><br></li>
                </ul>
                <p>
                  <strong>”Contact your administrator” emails:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines whether or not your logo is shown at the bottom of the emails sent to admins when users choose to contact you by clicking the “contact your administrator” link on the password reset UI.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset emails:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines whether or not your logo is shown at the bottom of the emails sent to users when they reset their passwords.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## 密碼管理行為
下表描述每個控制項如何影響使用者註冊密碼重設及重設其密碼的體驗。  您可以設定這些選項 **使用者密碼重設原則** 您的目錄區段 **設定** 索引標籤中 [Azure 管理入口網站](https://manage.windowsazure.com)。

> [AZURE.NOTE] 您使用的系統管理員帳戶必須擁有才能看到這些原則控制項所指派的 AAD Premium 授權。<br><br>這些原則控制項只適用於使用者重設其密碼，不適用於系統管理員。  **系統管理員擁有備用電子郵件和/或 microsoft，且無法變更為指定的行動電話的預設原則。**

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>原則控制</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>說明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響？</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="users-enabled-for-password-reset">
                  <p>使用者已啟用密碼重設</p>
                </div>
              </td>
              <td>
                <p>判對是否對此目錄中的使用者啟用密碼重設。 </p>
              </td>
              <td>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, no users can register their own challenge data.<br><br></li>
                  <li class="unordered">
                                                If set to yes, any end user in the directory can register challenge data by going to the registration portal at <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.<br><br></li>
                  <li class="unordered">
                    <strong>注意：</strong> 使用者必須擁有 Azure AD Premium 或 Basic 授權指派才可以註冊密碼重設。<br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, users see a message saying the must contact their admin to reset their password.<br><br></li>
                  <li class="unordered">
                                                If set to yes, users are able to reset their passwords automatically by going to  <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a>或按一下 <strong>無法存取您的帳戶</strong> 任何組織識別碼登入頁面上的連結。<br><br></li>
                  <li class="unordered">
                    <strong>注意：</strong> 使用者必須擁有 Azure AD Premium 或 Basic 授權指派才可以重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="restrict-access-to-password-reset">
                  <p>密碼重設限制存取</p>
                </div>
              </td>
              <td>
                <p>決定是否只允許特定群組使用者使用密碼重設。 (只有時才能看見 <strong>啟用密碼重設的使用者</strong> 設定為 <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>註冊入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, then all end users in your directory can register for password reset at <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a><br><br></li>
                  <li class="unordered">
                                                If set to yes, then only end users specified in the <strong>可以執行密碼重設的群組</strong> 控制項可以註冊密碼重設  <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a><br><br></li>
                </ul>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, then all end users in your directory can reset their passwords.<br><br></li>
                  <li class="unordered">
                                                If set to yes, then only end users specified in the <strong>可以執行密碼重設的群組</strong> 控制項可以重設其密碼。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="group-that-can-perform-password-reset">
                  <p>可以執行密碼重設的群組</p>
                </div>
              </td>
              <td>
                <p>決定允許哪個使用者群組使用密碼重設。 </p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>restrict access to password reset</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If no group is specified and you click <strong>Save</strong>, an empty group called <strong>SSPRSecurityGroupUsers</strong> will be created for you.<br><br></li>
                  <li class="unordered">
                                                If you’d like to specify your own group, you can provide your own display name.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If <strong>restrict access to password reset</strong> is set to <strong>yes</strong>, then only end users in this group will be able to register for password reset. <br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If <strong>restrict access to password reset</strong> is set to <strong>yes</strong>, then only end users in this group will be able to reset their passwords.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="authentication-methods-available-to-users">
                  <p>Authentication methods available to users</p>
                </div>
              </td>
              <td>
                <p>Determines which challenges a user is allowed to use to reset his or her password.</p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>users enabled for password reset</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                At least one option must be selected.<br><br></li>
                  <li class="unordered">
                                                We highly recommend enabling at least 2 options to give your users the most flexibility when resetting their passwords.<br><br></li>
                  <li class="unordered">
                                                If you are using security questions, we highly recommend you use them in conjunction with another authentication method, as security questions can be less secure than phone or email-based password reset methods.<br><br></li>
                </ul>
                <p>
                  <strong>Which directory fields are used?</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Office Phone corresponds to the <strong>Office Phone</strong> attribute on a user object in the directory.<br><br></li>
                  <li class="unordered">
                                                Mobile Phone corresponds to either the <strong>Authentication Mobile</strong> attribute (which is not publically visible) or the <strong>Mobile Phone</strong> attribute (which is publically visible) on a user object in the directory.  The service first checks <strong>Authentication Phone</strong> for data, and if there is none present, falls back to the <strong>Mobile Phone</strong> attribute.<br><br></li>
                  <li class="unordered">
                                                Alternate Email Address corresponds to either the <strong>Authentication Email</strong> attribute (which is not publically visible) or the <strong>Alternate Email</strong> attribute on a user object in the directory.  The service first checks <strong>Authentication Email</strong> for data, and if there is none present, falls back to the <strong>Alternate Email</strong> attribute.<br><br></li>
                  <li class="unordered">
                                                Security Questions are stored privately and securely on a user object in the directory and can only be answered by users during registration.  For security purposes, there is currently no way for an administrator to edit or see these answers.<br><br></li>
                  <li class="unordered">
                    <strong>Note: </strong>by default, only the cloud attributes Office Phone and Mobile Phone are synchronized to your cloud directory from your on-premises directory.  To learn more about which on-premises attributes are synced to the cloud, see <a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Attributes synchronized to Azure AD.</a><br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Affects which authentication methods are displayed when users are registering.  If you do not enable a given authentication method, users will not be able to self-register for that authentication method.<br><br></li>
                  <li class="unordered">
                    <strong>Note: </strong>users are currently not able to register their own office phone numbers; that authentication method must be defined by their administrator.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines which authentication methods a user can use as challenges for a given verification step.  For example, if a user has data in both the <strong>Office Phone</strong> and <strong>Authentication Phone</strong> fields in Azure Active Directory, then he or she can use either of these authentication methods to recover his or her password.<br><br></li>
                  <li class="unordered">
                    <strong>Note: </strong>users will be able to reset their password if and only if they have data present in the authentication methods you have enabled as an administrator.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-authentication-methods-required">
                  <p>Number of authentication methods required</p>
                </div>
              </td>
              <td>
                <p>Determines the minimum number of the available authentication methods a user must go through to reset his or her password.</p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>users enabled for password reset</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Can be set to 1 or 2 authentication methods required.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines the minimum number of authentication methods a user must register before being able to finish the registration experience.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Affects number of verification steps a user must go through before being able to reset a password.  A verification step is defined to be a user using one piece of authentication information (such as a call to their office phone, or an email to their alternate email) to verify their account.<br><br></li>
                  <li class="unordered">
                    <strong>Note:</strong> If a user does not have the required amount of authentication information defined on his or her account in order to be successful resetting his or her password in accordance with the policy you’ve set, he or she will see an error page which will direct them to request an administrator to reset his or her password.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-register">
                  <p>Number of questions required to register</p>
                </div>
              </td>
              <td>
                <p>Determines the minimum number of questions a user must answer when registering for the security questions option.</p>
                <p>(Only visible if the <strong>Security Questions</strong> checkbox is enabled).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Can be set to 3 – 5 questions required to register.<br><br></li>
                  <li class="unordered">
                                                Number of questions required to register must be greater than or equal to the number of questions required to reset.<br><br></li>
                  <li class="unordered">
                                                We recommend you set the number of questions required to register to be higher than the number required to reset so users have more flexibility when resetting their passwords.  This is also a more secure configuration because we will randomly select questions for the user to answer from the pool of all of the questions they have registered.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines the minimum number of questions a user must answer before the user is considered fully registered for password reset.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-reset">
                  <p>Number of questions required to reset </p>
                </div>
              </td>
              <td>
                <p>Determines the minimum number of questions a user must answer when resetting a password.</p>
                <p>
                  
                </p>
                <p>(Only visible if the <strong>Security Questions</strong> checkbox is enabled).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Can be set to 3 – 5 questions required to reset.<br><br></li>
                  <li class="unordered">
                                                Number of questions required to reset must be less than or equal to the number of questions required to register.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines the minimum number of questions a user must answer before the user’s password can be reset.<br><br></li>
                  <li class="unordered">
                                                At the time of password reset, this number of questions will be selected at random from the user’s total list of registered questions.  For example, if the user has 5 questions registered, 3 of those 5 questions will be selected at random when the user comes to reset a password.  The user must then answer all of these questions correctly before the password can be reset.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="knowledge-based-security-questions">
                  <p>Knowledge based security questions</p>
                </div>
              </td>
              <td>
                <p>Defines the pre-canned security questions your users may choose from when registering for password reset and when resetting their passwords.</p>
                <p>
                  
                </p>
                <p>(Only visible if the <strong>Security Questions</strong> checkbox is enabled).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                All knowledge-based questions will be localized into the full set of O365 languages based off of the user's browser locale.<br><br></li>
                  <li class="unordered">
                                                Up to 20 total questions can be defined (the sum of your custom and knowledge-based questions).<br><br></li>
                 <li class="unordered">
                                                Min answer character limit is 3 characters.<br><br></li>
                  <li class="unordered">
                                                Max answer character limit is 40 characters.<br><br></li>
                  <li class="unordered">
                                                Users may not answer the same question twice.<br><br></li>
                  <li class="unordered">
                                                Users may not provide the same answer to two different questions twice.<br><br></li>
                  <li class="unordered">
                                                Any character set may be used to define answers (including Unicode characters).<br><br></li>
                  <li class="unordered">
                                                The number of questions defined must be greater than or equal to the number of questions required to register.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines which questions a user is able to provide answers for when registering for password reset.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines which questions a user is able to use to reset a password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-security-questions">
                  <p>Custom Security questions</p>
                </div>
              </td>
              <td>
                <p>Defines the security questions your users may choose from when registering for password reset and when resetting their passwords.</p>
                <p>
                  
                </p>
                <p>(Only visible if the <strong>Security Questions</strong> checkbox is enabled).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Up to 20 total questions can be defined (the sum of your custom and knowledge-based questions).<br><br></li>
                  <li class="unordered">
                                                Max question character limit is 200 characters.<br><br></li>
                  <li class="unordered">
                                                Min answer character limit is 3 characters.<br><br></li>
                  <li class="unordered">
                                                Max answer character limit is 40 characters.<br><br></li>
                  <li class="unordered">
                                                Users may not answer the same question twice.<br><br></li>
                  <li class="unordered">
                                                Users may not provide the same answer to two different questions twice.<br><br></li>
                  <li class="unordered">
                                                Any character set may be used to define questions and answers (including Unicode characters).<br><br></li>
                  <li class="unordered">
                                                The number of questions defined must be greater than or equal to the number of questions required to register.<br><br></li>
                  <li class="unordered">
                                                Defining different questions for different locales is not supported for custom questions.  All custom questions will be displayed in the language in which you enter them in the administrative UI, even if the user's browser locale is different.  If you need these questions to be localized, please use the "knowledge based" questions instead.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines which questions a user is able to provide answers for when registering for password reset.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines which questions a user is able to use to reset a password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="require-users-to-register-when-signing-in">
                  <p>Require users to register when signing in?</p>
                </div>
                <p>
                  
                </p>
              </td>
              <td>
                <p>Determines if a user is required to register contact data for password reset the next time he or she signs in.  
                </p>
                <p>This capability works on any sign-in page that uses a work or school account.  Such pages include all of Office 365, the Azure Management Portal, the Access Panel, and any federated or custom-developed applications that use Azure AD to sign in.
                </p>
                <p>
                  
                </p>
                <p>Enforced registration will only apply to users who are enabled for password reset, so if you have used the "restrict access to password reset" feature and scoped password reset to a specific group of users, then only users in that group will be required to register for password reset when signing in.</p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>users enabled for password reset</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If you disable this feature, you can also manually send users to <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> to register their contact data.  <br><br></li>
                  <li class="unordered">
                                                Users can also reach the registration portal by clicking the <strong>register for password reset</strong> link under the profile tab in the access panel.<br><br></li>
                  <li class="unordered">
                                                Registration via this method can be dismissed by clicking the cancel button or closing the window, but users will be nagged every time they sign in if they do not register.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                This setting does not affect the behavior of the registration portal itself, rather, it determines whether or not the registration portal is shown to users when they sign in to the access panel.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-days-before-users-must-confirm-their-contact-data">
                  <p>Number of days before users must confirm their contact data</p>
                </div>
              </td>
              <td>
                <p>When <strong>require users to register</strong> is turned on, this setting determines the period of time which can elapse before a user must re-confirm their data. </p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>require users to register when signing in to the access panel</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Note: </strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Values between 0-730 days are accepted, with 0 days meaning that users will never be asked to re-confirm their contact data.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                This setting does not affect the behavior of the registration portal itself, rather, it determines whether or not the registration portal is shown to users when their contact data needs to be reconfirmed. <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="customize-the-contact-your-administrator-link">
                  <p>Customize the contact your administrator link?</p>
                </div>
              </td>
              <td>
                <p>Controls whether or not the contact your administrator link (shown to the left) that appears on the password reset portal when an error occurs or a user waits too long on an operation points to a custom URL or email address.</p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>users enabled for password reset</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Note: </strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If you enable this setting, you must choose a custom URL or email address by filling out the <strong>custom email address or url</strong> field immediately below this setting.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, users clicking the highlighted link will dispatch an email to the primary email address of all tenant administrators requesting that his or her password be reset.  This email is dispatched by following the logic below:<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
                                                        If there are password administrators, send the email to all password administrators, up to a maximum of 100 total recipients.<br><br></li>
                      <li class="unordered">
                                                        If there are no password administrators, send the email to all user administrators, up to a maximum of 100 total recipients.<br><br></li>
                      <li class="unordered">
                                                        If there are no user administrators, send the email to all global administrators, up to a maximum of 100 total recipients.<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
                                                If set to yes, this setting will customize the behavior of the highlighted link above to point to a custom URL or email address to which your users can navigate to get help with password reset.<br><br></li>
                  <li class="unordered">
                                                If you specify a URL, it will be opened in a new tab.<br><br></li>
                  <li class="unordered">
                                                If you specify an email address, we’ll create a mailto link to that email address.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-email-address-or-URL">
                  <p>Custom email address or URL</p>
                </div>
              </td>
              <td>
                <p>Controls the email address or URL to which the <strong>contact your administrator</strong> link points. </p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>customize contact your administrator link</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Must be a valid intranet or extranet URL or email address.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Changes where the <strong>contact your administrator</strong> link points.<br><br></li>
                  <li class="unordered">
                                                If you provide an email address, the link will become a “mailto” link to that email address.<br><br></li>
                  <li class="unordered">
                                                If you provide a URL, the link will become a standard href pointing to that URL which will open in a new tab.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="write-back-passwords-to-on-premises-directory">
                  <p>Write back passwords to on-premises directory</p>
                </div>
              </td>
              <td>
                <p>Controls whether or not Password Writeback is enabled for this directory and, if writeback is on, indicates the status of the on-premises writeback service.</p>
                <p>
                  
                </p>
                <p>This is setting is useful if you want to temporarily disable the service without re-configuring Azure AD Connect.</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                This control only appears if you have installed Password Writeback by downloading the latest version of Azure AD Connect and enabling the <strong>Password Writeback</strong> option under the <strong>optional features</strong> selection screen.<br><br></li>
                  <li class="unordered">
                                                If you have enabled Password Writeback and feel there is a configuration issue with the service, you can come to this tab and look at the <strong>password write back service status</strong> label to see if something is wrong.<br><br></li>
                  <li class="unordered">
                                                Statuses that can be shown are:<br><br><ul><li class="unordered"><strong>Configured </strong>– everything is working as expected<br><br></li><li class="unordered"><strong>Not configured</strong> – you have writeback installed, but we can’t reach the service, check to make sure you are not blocking outbound connections to 443 and try re-installing the service if you still have problems.<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If writeback is deployed and configured and this switch is set to <strong>no</strong>, then writeback will be disabled, and federated and password hash sync’d users will not be able to register for password reset their passwords.<br><br></li>
                  <li class="unordered">
                                                If the switch is set to <strong>yes</strong>, then writeback will be enabled, and federated and password hash sync’d users will be able to reset their passwords.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If writeback is deployed and configured and this switch is set to <strong>no</strong>, then writeback will be disabled, and federated and password hash sync’d users will not be able to reset their passwords.<br><br></li>
                  <li class="unordered">
                                                If the switch is set to <strong>yes</strong>, then writeback will be enabled, and federated and password hash sync’d users will be able to reset their passwords.<br><br></li>
                </ul>
              </td>
            </tr>
             <tr>
              <td>
                <div id="allow-users-to-unlock-accounts-without-resetting-their-password">
                  <p>Allow users to unlock accounts without resetting their password</p>
                </div>
              </td>
              <td>
              
              <p>Designates whether or not users who visit the password reset portal should be given the option to unlock their on-premises Active Directory accounts without resetting their password. By default, Azure AD will always unlock accounts when performing a password reset, this setting allows you to separate those two operations.</p>
              
              <p>If set to “yes”, then users will be given the option to reset their password and unlock the account, or to unlock without resetting the password. </p>
              
              <p>If set to “no”, then users will only be able to perform a combined password reset and account unlock operation.</p>

              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                In order to use this feature, you must install the August 2015 or later release of Azure AD Connect (v. 1.0.8667.0 or greater).<br><br><a href="http://www.microsoft.com/download/details.aspx?id=47594">Click here to download the latest version of Azure AD Connect.</a></li>
                        
                  <li class="unordered">
                    <strong>Note:</strong> In order to test this feature, you will need enable password writeback, and  use an account that is sourced from on-premises (like a federated or password synchronized user) and has a locked account.  Users who do not come from on premises and do not have a locked account will not see the option to unlock their accounts.</li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                After enabling this option, when a user with an on-premises account that is locked arrives at the password reset portal, he or she will be given the option to unlock their account without resetting their password.<br><br>Note that if you are using password writeback, accounts are already automatically unlocked when the password is reset, and that this option simply decouples those operations.<br><br>This is an especially useful option to enable if you find that many of your helpdesk calls are generated by account unlock requests.</li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## 密碼管理通知
下表描述每個控制項如何影響接收密碼重設通知之使用者和系統管理員的體驗。  您可以設定這些選項 **通知** 您的目錄區段 **設定** 索引標籤中 [Azure 管理入口網站](https://manage.windowsazure.com)。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>原則控制</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>說明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響？</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-admins-when-other-admins-reset-their-own-passwords">
                  <p>當其他系統管理員重設自己的密碼時通知系統管理員</p>
                </div>
              </td>
              <td>
                <p>決定當任何類型的其他系統管理員重設自己的密碼時，是否透過傳送至其主要電子郵件地址的電子郵件，通知所有全域管理員。</p>
              </td>
              <td>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, then no notifications will be sent.<br><br></li>
                  <li class="unordered">
                                                If set to yes, then all other global administrators will be notified when any single admin resets his or her own password.<br><br></li>
                  <li class="unordered">
                                                This notification is sent via an email to the primary email addresses of all other global admins in the organization.<br><br></li>
                </ul>
                <p>
                  <strong>範例：</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If this option was enabled when admin A resets his password, and there are 3 other admins in the tenant, B, C, and D, then admins B, C, and D would receive an email indicating admin A reset his password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-users-and-admins-when-their-own-password-has-been-reset">
                  <p>當使用者和系統管理員的密碼被重設時通知他們</p>
                </div>
              </td>
              <td>
                <p>決定重設自己密碼的使用者或系統管理員是否會收到電子郵件通知，表示他們的密碼已重設。</p>
              </td>
              <td>
                <p>
                  <strong>密碼重設入口網站：</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, then no notifications will be sent.<br><br></li>
                  <li class="unordered">
                                                If set to yes, then whenever a user or admin resets his own password, he or she will receive a notification indicating his or her password has been reset.<br><br></li>
                  <li class="unordered">
                                                This notification is sent via an email to the primary, and alternate (or authentication) email address of the user who reset his or her password.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/>
<br/>
<br/>

## 密碼重設文件的連結
以下是所有 Azure AD 密碼重設文件頁面的連結： 

* [**您自己的密碼重設**](active-directory-passwords-update-your-own-password.md) -深入了解如何重設或變更您自己的密碼為系統的使用者
* [**它的運作方式**](active-directory-passwords-how-it-works.md) -了解六個不同元件服務，以及每個未
* [**開始使用**](active-directory-passwords-getting-started.md) -了解如何讓使用者重設及變更雲端或內部部署密碼
* [**最佳作法**](active-directory-passwords-best-practices.md) -了解如何快速部署且有效管理組織中的密碼
* [**深入**](active-directory-passwords-get-insights.md) -了解整合式報告功能
* [**常見問題集**](active-directory-passwords-faq.md) -取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md) -了解如何快速移難排解服務的問題
* [**了解詳細**](active-directory-passwords-learn-more.md) -深入的技術詳細資料的服務的運作方式


[001]: ./media/active-directory-passwords-customize/001.jpg "Image_001.jpg"
