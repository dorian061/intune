---


---

<p>dorian在总结</p>
<h1 id="苹果，安卓，mac设备注册失败要怎么办"><span class="prefix"></span><span class="content">苹果，安卓，MAC设备注册失败要怎么办</span><span class="suffix"></span></h1>
<h2 id="对于苹果设备"><span class="prefix"></span><span class="content">对于苹果设备</span><span class="suffix"></span></h2>
<p>苹果设备一切的前提条件是需要 apple MDM push certificate，这个token的有效期是一年，在有效期之后有一个月的关键期，在关键期内renew是没有影响的。如果说已经过期了，那么尽管在intune portal中还能看到iOS设备，但实际上intune也失去了管理这些设备的能力。只能将所有设备删除后重新绑定apple MDM push certificate，将设备注册到intune。</p>
<ul>
<li>
<p>BYOD<br>
如果是download management profile失败的话，大概率是网络的问题，我们就去建议用户换一个网络试试，或者说看看其他设备在相同的网络下是否也都是一样的情况。如果说是install management profile这个阶段失败或者说检查设置这里超过十分钟的话，可以看看这台设备是否以前注册过Intune。如果说是一台全新的设备，那么可以去收集一下company portal log。可以通过company portal app里的setting and help去上传日志，上传完之后会有一个incident id，拿到id之后可以去后台（powerlift）用这个id把log导出来，然后在log里搜关键词WPJ（work place joined）就可以看到一些相关的信息</p>
</li>
<li>
<p>apple configurator<br>
需要用数据线连接到mac，在mac上的apple configurator 软件上完成<br>
直接注册是首先在intune portal中创建配置文件，导出url到apple configurator中，下载配置文件。然后用apple configurator把配置文件传输到iOS/iPadOS设备上<br>
或者通过设置助理来注册，上传csv文件到intune，然后把配置文件assign给设备，然后把配置文件的URL导出，在apple configurator上下发给设备。</p>
</li>
<li>
<p>ADE<br>
如果是公司批量购买的设备应该一开始就在ABM/ASM下，也可以手动把设备添加到ABM/ASM。用ADE方式把设备注册到intune首先需要把ABM/ASM的token绑定到intune上，然后把ABM/ASM上的设备分配到创建的p7m token下。设备同步到intune之后，点进设备创建配置文件。</p>
<ul>
<li>
<p>toubleshooting流程</p>
<ul>
<li>在OOBE阶段失败，首先看token的状态，token下是否有当前这台设备，然后需要一个配置文件截图，具体是怎么配置的（user affinity/authentication method/supervised）</li>
<li>假设选的认证方式是modern authentication的话，然后在输入Microsoft账号密码的阶段就报错了的话，这时候我们就可以去收一个console log。收这个log需要用数据线把设备和mac连接，然后用mac的一个内置应用console。打开console然后点左上角的actions，把debug和info message勾选上，然后点record，然后把问题复现之后再stop。这时候会出现很多log的内容，因为它不能直接导出来，所以我们可以把这些内容复制粘贴到TXT文档上拿去分析。这里也可以用Windows来连接设备去收log，不过会用到一个第三方软件叫xlog，所以一般还是推荐客户去用mac来收。</li>
<li>如果选择的验证方式是company portal，需要在进入主页面后在company portal中登录组织账号，出现问题的话可以收company portal log。</li>
</ul>
</li>
</ul>
</li>
</ul>
<h2 id="对于安卓设备"><span class="prefix"></span><span class="content">对于安卓设备</span><span class="suffix"></span></h2>
<p>像Device Administrator的话首先要确保device platform restriction创建了一条block掉AE的restriction，并且把这条的优先级调到最高，否则在同等条件下，AE的优先级永远高于DA，只有在将AEblock掉或者因为GMS的原因AE不可用的时候，DA才会变成首先注册到intune 的方式。如果DA出现问题也是可以去通过company portal的incident id来收log的。</p>
<p>android BYOD设备不需要恢复出厂设置，可以直接下载company porta登录intune账号，然后下载配置文件，这个注册出现了问题也可以收company portal log。<br>
如果说是AE的一些设备，像dedicated，fully managed和corporate owned，这些没有company portal这个应用，不过他们可以通过Microsoft Intune这个应用来收log，从这里收上来的log和从company portal上收上来的log没有什么区别。如果说是在扫QR code的阶段出现问题话，就得搜debug log，这个和console log的形式差不多，也会用到一个USB，然后会需要在settings里的developer options里边复现问题然后收report再发到邮箱里。<br>
除此之外对于dedicated设备，如果是在展台模式下，在manage home screen中的展示的应用，首先需要添加到google 商店中，并且要新建一个android平台的device configuration，选择manage home screen需要展示的app，可以调节这些app 的layout。<br>
manage home screen出现了问题，那么侧拉多次会出现一个弹窗，可以收MHS的log。</p>
<h2 id="对于mac设备"><span class="prefix"></span><span class="content">对于MAC设备</span><span class="suffix"></span></h2>
<p>MAC是不能直接在store里下company portal的，它可以通过官方的一个网页下载，这个网页链接在Microsoft的文档里有给到 <a href="https://go.microsoft.com/fwlink/?linkid=853070">https://go.microsoft.com/fwlink/?linkid=853070</a> 然后下载下来之后再去收company portal log就行了。</p>
<p>也可以直接注册，在mac上直接下载配置文件，安装到mac上。</p>
<p>MAC也可以用ADE方式注册到intune，方式和iOS/iPadOS没有区别。</p>
<h1 id="windows-byod注册"><span class="prefix"></span><span class="content">windows BYOD注册</span><span class="suffix"></span></h1>
<p>首先确定windows版本，crtl+R winver<br>
Windows10，1607及以后版本的在Windows store中下载company portal,连接到组织账号，出错的话可以收company portal log。<br>
Windows10，1511及之前版本的在设置中，account-add a work or school account-sign in，之后的版本也可以用这种方式注册，这属于automatic enrollment的BYOD，首先要在entra ID portal中查看mobility，MDM user scope有没有打开为all<br>
但如果是家庭版本的设备，只能register到entra，在setting中输入组织账号，首先去entra中看有没有 Microsoft entra registered，然后再回到intune portal中查看状态，如果注册不成功会报错，在详情页上得到error code。<br>
或者也可以在event viewer里面device management provider -admin，查看到注册成功或失败的event id。</p>
<h1 id="autopilot失败怎么办"><span class="prefix"></span><span class="content">Autopilot失败怎么办</span><span class="suffix"></span></h1>
<h2 id="整个autopilot都没生效"><span class="prefix"></span><span class="content">整个autopilot都没生效</span><span class="suffix"></span></h2>
<p>首先需要的内容是设备的序列号（win+R，cmd，<code>wmic bios get **serialnumber**</code>），还有根据序列号查找intune portal&gt;device&gt;windows&gt;Windows enrollment&gt;autopilot&gt;device， 这个序列号device的profile status是不是assigned<br>
在设备上用<code>mdmdiagnosticstool. exe -area AutoPilot -cab %temp%|%COMPUTERNAME%.cab</code> 或者直接在setting中的accout&gt;access work or school处的related settings直接导出cab包，发送给我们，然后看到autopilot.json文件，如果确实有autopilot deployment profile文件到了设备上，会显示profile name，否则的话就会出现报错。<s>也可以请求顾客给我们autopilot deployment profile页面的截图，因为虚拟机无法使用self-deploying和preprovision模式注册。（如果用虚拟机注册preprovison会在ESP的device prepare的secure your hardware部分报错）</s><br>
如果没有部署到设备上，我们可以询问客户，这台设备是不是windows10或11pro或企业版，这期间设备的hardware有没有做过更改，不清楚的话我们可以把硬件哈希的csv文件重新导出，上传到portal将整个autopilot流程再现一遍，确保上传到intune portal的csv文件和当前设备的硬件哈希是匹配的。</p>
<h2 id="在device-preparation阶段失败"><span class="prefix"></span><span class="content">在device preparation阶段失败</span><span class="suffix"></span></h2>
<ul>
<li><strong>Secure hardware</strong>这里失败的话，说明设备的某些硬件要求没达到标准，这里更多的是针对self-deploying和pre-provisioned的设备，他们有一个tpm2.0的要求</li>
<li><strong>join organization’s network</strong>这里失败的话，说明设备没法join到AAD，这里也是针对self-deploying和pre-provisioned的设备，标准的user-driven在进入到ESP这个界面时就已经完成了这个操作了。这里失败的话就得去Azure portal上看Device settings，里面有一个Users may join devices to Microsoft Entra，这里是否设置成了all，设置成some的话这台设备是否有包含在里面。另外可以看maximum number of devices per user，这里是否超过了限制，一般来说recommend的是20台，不过用户也可以自定义到1-unlimited。</li>
<li><strong>register device for mobile management</strong> 需要收集的信息就包括，esp报错页面的截图，autopilot deployment profile页面截图，cab包。<br>
这里失败的话，说明设备没法enroll到intune，这里就可以去通过azure或者intune portal上看是否MDM user scope是设置成all的，如果设置成some了的话，这台设备是否包含在内。另外还可以在intune portal上看device platform restriction,看是否block了MDM或者设置了version要求。同时可以看看device limit restriction是否超过限制了，非DEM的情况下，一个standard user最多是可以enroll15台。另外还可以看看这些都检查完之后，可以让客户再去敲一个<code>MDMDiagnosticstool -area Autopilot -cab</code>的命令去收集log，收到之后我们在cab包里主要看的是provier-admin的event log，打开之后看里面是否有event id 75或76，失败的话我们更多的是找76，找到了的话它旁边可能会给到一些有用的error code或者error message，我们就能进一步去看看是什么原因。如果这两个log我们都没看到的话，那说明这个注册可能根本就没触发，我们可就需要进一步跟客户去确认是否是网络或者设备层面的原因。网络的话可以看用户在相同的网络下其他设备是否有注册成功的，另外用户换一个网能否解决问题。如果还是不成功的话，可以尝试用netsh抓包。</li>
</ul>
<h2 id="在device-setup阶段失败"><span class="prefix"></span><span class="content">在device setup阶段失败</span><span class="suffix"></span></h2>
<ul>
<li>
<p><strong>security policies</strong>这个阶段失败的话<br>
首先收集的信息是，ESP界面报错截图，portal中当前设备policy的状态，cab包。<br>
portal中在device下找到当前设备，点进去，找到device configuration，看到每条policy的状态，或者我们可以在kusto中查看每一条policy的部署状态。我们也需要让客户用shift+f10去敲一个MDMDiagnosticstool area autopilot cab的命令去把log收上来。<br>
然后configuration policy这边我们可以找到出问题的策略，我们就可以去打开provider-admin的event log，在这里面通过policy的关键词去搜索，看是否出现一些error message和error code去锁定具体原因，也可以在html文档中查看configuration policy 的部署状态。<br>
我们可以进一步要求客户提供这条policy配置页面的截图，并且根据官方文档提供的这条policy需求的前提条件问客户是否都满足，比如说configuration assignment这边是下发给user还是device，因为有些policy要求可能只能下发给user，或者说有些只能下发给device。如果说前提条件不满足的话，那我们建议客户移除掉这条策略。如果说前提条件满足，我们可以根据官方文档看一下配置是否正确无误。都没有问题的话，那么我们可以帮客户测试一遍这条策略，出现问题的话可以咨询我们这边相关的功能组。</p>
</li>
<li>
<p><strong>certificate profiles</strong>这个阶段失败的话，说明SCEP profile的install可能出现问题了，我们就需要看对应的profile设置和assignment是否正确。</p>
</li>
<li>
<p><strong>network connections</strong>这个阶段失败的话，就需要去确认一下网络层面的原因。试着更换一个网络，或者可以用<code>netsh trace start scenario=internetClient capture=yes maxSize=2048</code>这个命令检查网络状况<br>
<code>netsh trace stop</code> 停止检查。确认是网络的问题可以转交给网络组解决问题。</p>
</li>
<li>
<p><strong>Apps</strong><br>
这个阶段失败的话，说明我们在intune上部署的app要么下载不成功，要么安装不成功。我们得先去确认具体是由哪一个app导致的失败，用shift+f10唤出命令行，用<code>mdmdiagnosticstool. exe -area AutoPilot -cab %temp%|%COMPUTERNAME%.cab</code> 收cab包，然后跑一个<code>Get-AutopilotDiagnostics cab</code>的命令去获取一条autopilot的时间线，这里我们也可以看到具体是在哪一个app上失败的以及失败的时间。也可以看看是否有LOB和win32的app在一起推，因为在autopilot阶段一起推的话他们installer的组件会起冲突，也可能会导致一个失败。总之确认好是哪个app后就要去看对应的信息.</p>
<ul>
<li>
<p><strong>LOB app</strong>，首先确定是安装问题还是下载问题</p>
<ul>
<li>下载问题<br>
在路径<code>C：\Windows\System32\config\systemprofile\AppData\Local\mdm</code>下没有找到下载的msi安装包可以判定为下载失败<br>
在注册表中定位到<code>HKLM\SOFTWARE\Microsoft\EnterpriseDesktopAPPManagement</code>，面向设备的应用定位到s-0000···&gt;MSI，用appid定位到下载失败的msi应用；面向用户的应用定位到userSID/MSI。用appid定位到下载失败的msi应用<br>
再次下载msi文件可以将EnforcementRetryCount 和 EnforcementRetryIndex 设置为 （0），并将 LastError 和 Status 设置为空字符串。</li>
<li>安装问题<br>
已经在路径下发现了msi的安装包，那说明是安装的问题，可以用这个命令手动静默安装，并且附带一个日志文件。<code>msiexec /I &lt;包名&gt;.msi /l*v %temp%\msiverbose.txt /qn</code></li>
</ul>
</li>
<li>
<p><strong>Office</strong><br>
我们可以去看Windows Temp或者System Temp里看有没有tmp.exe和一个.xml（或者open document，总之会有两个文件）的文件，如果有的话证明我们的Intune是部署下来了的，应用没有安装成功可能是xml文件本身写的有问题，可以定位到该文件夹下，用cmd命令行手动download并且configure这个xml文件看看有没有问题，有问题的话反馈给客户是xml文件本身的问题。或者可能是网络的问题，ODT离线包没有办法在线下载，可以换一个网络尝试一下。</p>
</li>
<li>
<p><strong>store new</strong>或者<strong>win32</strong><br>
我们可以去看IME Log。对于没安装上的app，问题更多可能是出现在applicability，download，unzipping，和installation这四个阶段。</p>
<ul>
<li>
<p>applicability的话就是检测设备的一些硬件或者操作系统是否符合用户在intune portal上设置的，比如说某个app会要求用户是32还是64bit，最低的operating system是多少，或者说要求一些disk space，physical memory或者processor需要达到多少。只有这个阶段通过了才能进入到下载阶段</p>
</li>
<li>
<p>download阶段的话就得看这个下载是否已经开始，如果开始了的话，我们应该是能在<code>C:/program files(x86)/Microsoft Intune Management Extension/Content</code>里找到一个incoming的folder，这里面应该也会有一个bin的文件，我们也可以搜关键词，比如说start downloading来确认是否开始下载。如果显示下载报错，我们可以搜索关键词download url来让客户手动下载看是否能下载下来，如果手动也不能下下来那可能跟网络有一定的关系。</p>
</li>
<li>
<p>unzip阶段，我们可以通过搜索关键词start unzipping来确认是否解压的过程开始了，如果解压失败的话在这条信息的附近应该也是能看到一些原因的。比如说如果显示unauthorized access什么什么的就说明用户可能在这个解压的过程中把那个zip包点开从而导致了一个解压失败。这个阶段是在一个叫staging的文件夹进行的，如果解压成功了的话他会把文件转移到Windows IME Cashe的文件夹里，我们也可以去这两个地方确认到底卡在了哪一步。</p>
</li>
<li>
<p>installation<br>
确认到是哪个app出现问题之后我们可以进一步要求客户提供app deployment页面的截图是如何配置的。</p>
<ul>
<li>store new<br>
我们需要用winget info的命令来确认一下winget或者说是package manager的版本，因为v1.7以前的版本话是有问题的，也可能会导致报错。然后在powershell中直接手动winget install，这里的话会需要一个package identifier，这个package dentifier可以在intune portal的app property里找到。如果手动跑winget的命令都安装失败的话，那可能就是winget本身的问题，移交相应的产品组。</li>
<li>win32的app，我们需要拿到用户的安装卸载命令和detection rule，还有install behavior是system还是user。安装包。<br>
如果是system的话，需要用到pstool来模拟一个system context然后去跑命令看是否能手动安装。如果手动安装出现问题的话，可能是安装命令的问题，可以建议客户去应用官网或者用户手册找到正确的安装命令。如果确认安装命令是正确的，那么我们可以移除掉安装命令中的静默命令然后看安装过程中具体的报错是什么。<br>
如果是user的话那么直接打开powershell，去运行安装命令，接下去的流程和system上下文类似。<br>
如果说是已经安装上了的app出现报错，那大概率是post-install detection这个阶段出问题了。这时候就需要先去确认用户设置的detection rule是什么。是script还是manual configure，如果是script的话，我们要去IMElog里搜exit code，看最后的value是否是0，如果不是0的话，那可能script本身也需要再去看一看。如果是manual configure，并且detection method设置的是file or folder exists的话，我们就要去检查，第一是否路径是否是正确的，其次检测的文件与文件名是否是正确的。</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
<h1 id="gpo注册成功与否"><span class="prefix"></span><span class="content">GPO注册成功与否</span><span class="suffix"></span></h1>
<p>GPO第一步要确认hybrid环境有没有配置好，在命令提示符下输入dsregcmd /status得到当前设备状态。<br>
并且要确保mdm URL那几栏是有内容的，如果URL是空的，虽然能够在intune portal上看到设备，但是mdm和owner都是空的，这个时候还没有完全完成inune注册，需要确认的是，在server上的active directory domain and trusts下添加组织的UPN，然后将user的UPN更改为组织UPN，确保user的upn和portal中user的upn是一致的。用portal中user的完整名称登录到客户端设备。<br>
然后我们可以查看task scheduler中是否创建了一个GPO enroll的任务，如果没有的话尝试gpupdate /force，并且重新启动设备。<br>
如果还没有检查，server端的创建的组策略是否link到了正确的OU，并且组策略在administrat templates&gt;windows components&gt;MDM下打开了enable automatic MDM enrollment，选择了user credential<br>
最后去event viewer中查看enroll的最终结果。<br>
如果想要看到完整的组策略报告，还可以在命令行输入gpresult /r。</p>
<h1 id="conditional-access"><span class="prefix"></span><span class="content">conditional access</span><span class="suffix"></span></h1>
<ol>
<li>看先决条件是否满足（Microsoft entra ID p1 licenses，或Microsoft 365 business premium licenses），在启用conditional access的时候需要先关闭intune默认的保护策略。</li>
<li>看sign-in logs最长可以看到一个月前的</li>
<li>Correlation ID快速确定是哪一次登陆失败，从设备登陆页面的报错提示可以看，网页版是interactive，app内部是non-interactive，app内部报错无法查看详细信息那么就用时间点及时查看。</li>
<li>由于cap是entra的服务所以找Entra Device ID</li>
<li>如果entra和intune中的compliant状态不一致看audit log，update的记录</li>
</ol>
<h1 id="app-deployment"><span class="prefix"></span><span class="content">App deployment</span><span class="suffix"></span></h1>
<h2 id="安卓"><span class="prefix"></span><span class="content">安卓</span><span class="suffix"></span></h2>
<ul>
<li>AE<br>
通过google play推应用，稍微有点儿区别的就是dedicated中MHS的显示（上文有提到），用intune来收log</li>
<li>其他<br>
LOB、web-link，可以上传到google play中再推到设备上<br>
DA就用android store app这种类型推应用，用company portal收log</li>
</ul>
<h2 id="ios"><span class="prefix"></span><span class="content">iOS</span><span class="suffix"></span></h2>
<ul>
<li>vpp<br>
确保vpp token的状态是健康的，在vpp token下推应用，可以选择user license或device license，区别在于如果是device license那么assign时选择available就是一个无效的操作</li>
<li>其他</li>
</ul>
<h2 id="windows"><span class="prefix"></span><span class="content">Windows</span><span class="suffix"></span></h2>
<ul>
<li>win32</li>
<li>store （new）</li>
<li>LOB</li>
<li>office</li>
<li>PowerShell<br>
-首先在intune portal&gt;device&gt;scripts and remediation&gt;platform scripts&gt;create，上传PS1文件（&lt;200kb），选择Run this script using the logged-on credentials， no的话就是在系统上下文运行。script如果运行失败只会重复尝试三次，而且powershell脚本文件会在30分钟后过期。
<ul>
<li>troubleshooting<br>
首先可以在intune portal中看到一个powershell脚本下所有设备的状态，是否成功部署powershell脚本。可以要一个powershell脚本配置页面的截图，确认是用户环境运行还是系统环境运行。另外收一个cab包。<br>
设备端首先从以下路径下载并运行power shell脚本<code>C:\Program Files (x86)\Microsoft Intune Management Extension\Policies\Scripts\UserGUID_ScriptGUID.ps1</code><br>
运行结果可以在以下路径找到<code>C:\Program Files (x86)\Microsoft Intune ManagmentExtension\Policies\Results\UserGUID_ScriptGUID.output|.error|.timeout</code><br>
运行成功后results文件会被自动清除，这个时候可以去注册表中查看posershell脚本的状态，<code>HKLM\SOFTWARE\Microsoft\IntuneManagementExtension\Policies\UserGUI D\ScriptGUID</code>，找到对应policy ID的power shell脚本，会有脚本的下载次数，运行结果和error code。<br>
然后我们可以查看IME log，用policy ID去找到这条出错的powershell脚本，看到他的error message。<br>
我们还可以手动运行这个powershell脚本，如果是在系统上下文，我们就可以借助pstool看看运行报错内容，如果是用户上下文，那么久直接打开powershell.exe，运行PS1文件。</li>
</ul>
</li>
</ul>
<h1 id="app-protection-app-configuration"><span class="prefix"></span><span class="content">App protection&amp; app configuration</span><span class="suffix"></span></h1>
<h2 id="app-protection"><span class="prefix"></span><span class="content">App protection</span><span class="suffix"></span></h2>
<p>首先确定有没有sign out ，sign in一次，手动sync查看配置是否生效，然后根据想要达到的最终需求查看policy配置页面的截图，看具体的设置有没有配对。App protection看到properties页面，policy managed apps分别是哪些，在data protection中想要生效的应用有没有被涵盖到，在assign的时候有没有分配到正确的用户，在设备上登录的user是不是在assign到的用户范围之中。确认完这些信息之后，如果没有问题那么去edge中收集MAM log，输入 about: intune help，得到MAM log然后搜索关键词查找到对应的值参照文档<a href="https://learn.microsoft.com/en-us/mem/intune/apps/app-protection-policy-settings-log">查看应用保护策略日志 - Microsoft Intune |Microsoft学习</a>看看想要配置到的值是不是对应到了，比如说block应该是value=0等等，如果这个地方值对不上，那可能是intune portal本身出现了问题，联系产品组，如果是值已经对应上了，最终app还是没有生效，那就怀疑是app本身的问题，可能没有intune SDK集成，或者集成失效，这个时候去找对应的app 产品组。</p>
<h2 id="app-configuration"><span class="prefix"></span><span class="content">App configuration</span><span class="suffix"></span></h2>
<p>同样的首先需要sign out ，sign in一次，手动sync查看配置是否生效<br>
需要注意的是选择managed device，并且设备平台为iOS时，通过intune下发的应用需要绑上mamupn才能够被识别为managed。<br>
一些常见的microsoft应用会有官方文档指导配置设置，如果是一些其他的应用可以找应用开发商看具体的配置要求。<br>
依照官方文档或应用开发商提供的文档完成setting配置后，确认在assign的时候有没有分配到正确的用户，在设备上登录的user是不是在assign到的用户范围之中。<br>
如果都没有问题，那就去收MAM log。</p>
<h1 id="configuration--compliance-policy"><span class="prefix"></span><span class="content">configuration &amp; compliance policy</span><span class="suffix"></span></h1>
<p>首先都要确认intune portal中到底配置了哪些policy，policy的状态，还有policy的properties具体内容</p>
<ul>
<li>安卓设备<br>
主要看company portal log中的 OMADA log</li>
<li>iOS设备<br>
可以看managed profile中新添加了哪些settings</li>
<li>Windows设备<br>
我们可以找用户先确认一下最近是否有修改过policy的一些设置，如果有的话我们的范围就能迅速缩小，知道是哪个policy失败之后可以去provider-admin看他的状态，<s>然后去注册表根据这条policy的名字看具体的值</s></li>
</ul>
<h1 id="rbac"><span class="prefix"></span><span class="content">RBAC</span><span class="suffix"></span></h1>
<p>在intune中的role和entra ID上的role是两套平行的系统，intune上的权限更加细分，entra上的权限给的更加笼统。<br>
像intune上面会细分，update、read等等权限，而entra上可能只有一个manage（yes/no）<br>
如果已经在entra ID中分配了一个较大的权限，再在intune portal中细分权限不会生效，权限叠加只会按照最高权限的许可来执行<br>
在intune中自定义创建一个role或者选择一个已有的role分配给选择包含要授予其权限的用户的组，接着scope group选择可以管理的用户和设备组，然后给这些用户分配一个scope tag，用户只可访问有相同scope tag的资源。</p>
<h2 id="role不生效时的troubleshooting流程"><span class="prefix"></span><span class="content">role不生效时的troubleshooting流程</span><span class="suffix"></span></h2>
<p>首先看role中monitor下的的my permission，如果已经配置了权限仍然没有显示的话，可以去查administrator licensing，可以尝试给user分配一个license，一般来说在没有license的情况下也是可以配置权限的，但这部分经常会出问题，可以和intune产品组反映。</p>
<h2 id="范围标记（scope-tag）"><span class="prefix"></span><span class="content">范围标记（Scope Tag）</span><span class="suffix"></span></h2>
<p><strong>定义</strong>：</p>
<ul>
<li>范围标记是一种标签，用于标记和区分不同的对象（如设备、配置文件等）。</li>
<li>角色分配定义：<br>
哪些用户被分配到角色<br>
他们可以看到哪些资源<br>
他们可以更改哪些资源。<br>
您可以将自定义角色和内置角色分配给用户。<br>
若要分配 Intune 角色，用户必须具有 Intune 许可证。 若要查看角色分配，请选择“Intune”&gt;“租户管理”&gt;“角色”&gt;“所有角色”&gt;选择角色&gt;“分配”&gt;选择分配。<br>
成员：列出的 Azure 安全组中的所有用户都有权管理“作用域（组）”中列出的用户/设备。<br>
作用域（组）：作用域组是用户或设备的 Microsoft Entra 安全组，或两者兼而有之，该角色分配中的管理员只能对其执行操作。例如，将策略或应用程序部署到用户或远程锁定设备。这些 Microsoft Entra 安全组中的所有用户和设备都可以由成员中的用户进行管理。<br>
范围（标签）：成员中的用户可以看到具有相同范围标签的资源。</li>
</ul>
<p><strong>作用</strong>：</p>
<ul>
<li>范围标记主要用于角色分配和资源的可见性控制。</li>
<li>管理员可以基于范围标记来控制哪些资源（如设备、配置文件等）对哪些用户或管理员可见和可管理。</li>
<li>通过分配不同的范围标记，可以将不同的对象分配给不同的管理员，从而实现资源分离和管理权限分配。</li>
</ul>
<p><strong>使用场景</strong>：</p>
<ul>
<li>在大型企业中，有不同的管理员负责不同部门的设备管理。可以通过范围标记将设备、配置文件等资源分配给特定的管理员，从而实现精细化的权限控制。</li>
<li>例如，给销售部门的设备分配“Sales”范围标记，给IT部门的设备分配“IT”范围标记，然后相应地给不同的管理员分配不同的范围标记权限。</li>
</ul>
<p><strong>示例</strong>：</p>
<ul>
<li>将某些策略和配置文件标记为“US-East”，使得只有负责US-East区域的管理员可以查看和管理这些策略和配置文件。</li>
</ul>
<h1 id="scep"><span class="prefix"></span><span class="content">SCEP</span><span class="suffix"></span></h1>
<h2 id="证书申请流程"><span class="prefix"></span><span class="content">证书申请流程</span><span class="suffix"></span></h2>
<p>首先intune下发一个配置文件给设备，设备通过配置文件中NDES的URL找到NDES服务器，然后向NDES发送三条指令</p>
<ul>
<li>GetCACAPS： get CA cap是验证CA是否适合颁布这张证书</li>
<li>GetCACERT： 用于验证证书链上的trust CA是否已经安装到设备上</li>
<li>PKIOperation</li>
</ul>
<p>NDES Server拿到了这个request之后，会先把这个request递交给connector，connector要做三件事：receive, download, and verify request，request验证成功后， （NDES安装完成后，会自动安装一个application pool到你的server上），NDES上的IIS将request递交给CA，CA颁布证书。request一般只要成功递交到CA上，CA就会颁布证书，颁布证书也是通过http，在pki operation部分，通过IIS，将证书以下载的形式发布到device端。</p>
<h2 id="troubleshooting流程"><span class="prefix"></span><span class="content">troubleshooting流程</span><span class="suffix"></span></h2>
<p>首先看流程第一步从intune下发到设备上的文件</p>
<ul>
<li>可以从intune portal看SCEP文件下发状态，或者从kusto和rave查看</li>
<li>注意peofile的分配，确认是否trust cert和scep cert被分配给了同一个用户或者同一个设备。</li>
</ul>
<p>NDES server</p>
<ol>
<li>看IIS log，前面流程说到device会下发几个指令，get CA cap, get CA cert, pki operation，验证完CA cap和CA certificate，才会把request递交给intune certificate connector，<code>C:\inetpub\logs\LogFiles\W3SVC1\u_exlog</code>，如果能在IIS log里看到get CA cap, get CA cert，post并且值都是200，就可以看CA connector的日志了<br>
如果IIS中出现了报错，非200的报错，检查server url是否可以获取到403 forbidden的报错。</li>
<li>如果是poxy的错误</li>
<li>最后看cert connector相关的日志，Event viewer&gt;Application&amp;EventService &gt;Microsoft&gt;Intune&gt;CertificateConnector，要admin和operational两份，event id从4001到4010</li>
</ol>
<h1 id="co-management注册不成功"><span class="prefix"></span><span class="content">Co-management注册不成功</span><span class="suffix"></span></h1>
<p>我们先要找用户确认是注册未触发导致的失败，还是说是触发了但是enroll这个过程失败了。在更深入的检查之前，我们也可以先向用户确认一下prerequisites是否都满足，比如说configuration manager的版本是否支持，用户是否有EMS的license，如果是SCCM to Intune的话，那么device是否是hybrid aad joined的，如果是hybrid joined的话，再看看是否是pending status，如果是pending的话也是会导致注册失败的。MDM User scope如果不是All的话，也建议用户先开成All看看是否能成功。还有就是device restrictions这里也可以确认是否设限。</p>
<p>首先我们去client 端，在control panel上找到configuration manager，看到configuration manager properties里的configuration，看一下ComgmtSettingpilotautoenroll这条策略是否存在。<br>
如果不存在，那我们需要去SCCM console下查看automatic enroll in intune这个策略是all，还是pilot，如果是pilot的话那么这台设备是否包含在pilot设备集合中。<br>
确认完之后回到client端看一下这条策略的状态是compliant还是non-compliant，如果是compliant说明已经注册到intune了，如果是non-compliant那么我们可以evaluate重新触发注册到intune，如果还是non-comliant那么我们可以在这个界面点view report，进一步去收provider-admin的log还有SCCM的handler log，在路径<code>C:\Windows\CCM\Logs - Check Co-managementhandler.log</code>下。因为有时候他并不是立即触发enrollment的，他会有一个up to 5 days的randomized time，对于老一点的版本，我们可以搜关键词queuing enrollment timer来看他具体是schedule到什么时候enroll的，如果是新一点的版本的话就是搜schedule time。还有一种情况可能是要等到下一个user log in了他才能触发enroll，这时候就只需要重启一下设备就能触发了。如果这些都做完之后还是没用的话，我们就可能需要找SCCM的同事去寻求帮助。</p>
<p>co-management注册成功，部署策略或应用失败，首先在client端的control panel下点进configuration manager&gt;general，然后查看work load value，把这个value</p>
<h1 id="tools-and-logs"><span class="prefix"></span><span class="content">tools and logs</span><span class="suffix"></span></h1>
<h2 id="dfm"><span class="prefix"></span><span class="content">DFM</span><span class="suffix"></span></h2>
<ul>
<li>接case和客户联系用</li>
<li>DFM写第一封邮件<br>
自我介绍<br>
对这个case的理解<br>
需要收集的信息<br>
附上outlook邮箱</li>
<li>电话号码区号<br>
+91 印度（印度有team负责）<br>
+1美国<br>
+855香港<br>
+61澳大利亚</li>
</ul>
<h2 id="kusto"><span class="prefix"></span><span class="content">kusto</span><span class="suffix"></span></h2>
<p>会列出所有的policy和app 的状态（除win32和store（new））<br>
Win32和store（new）的状态会在底下的intune management extension中显示出来<br>
Device compliance的状态和原因<br>
所有内容都只保持30天</p>
<h2 id="ac"><span class="prefix"></span><span class="content">AC</span><span class="suffix"></span></h2>
<p>搜索case id看一些基础的状态和成员</p>
<ul>
<li>user、group的成员，Device ID是AAD 的device ID<br>
Is compliant<br>
Is managed<br>
Profile type</li>
<li>AAD中的audit log</li>
<li>Sign-in log</li>
</ul>
<h2 id="assist-365"><span class="prefix"></span><span class="content">Assist 365</span><span class="suffix"></span></h2>
<p>Device，搜索intune device id或者用户UPN<br>
enrollment<br>
MAM，user，app有没有安装，最后一次同步的时间</p>
<h2 id="powerlift"><span class="prefix"></span><span class="content">powerlift</span><span class="suffix"></span></h2>
<p>用companyportal的incident ID去搜<br>
MAM log<br>
MDM log</p>
<h2 id="logs"><span class="prefix"></span><span class="content">logs</span><span class="suffix"></span></h2>
<h3 id="mdm-log"><span class="prefix"></span><span class="content">MDM log</span><span class="suffix"></span></h3>
<p>portal上device直接collect diagnostics(cooperate owned才会有)</p>
<h3 id="procmon"><span class="prefix"></span><span class="content">procmon</span><span class="suffix"></span></h3>
<p>设备上所有的在运行程序的所有操作记录下来</p>
<h3 id="odc"><span class="prefix"></span><span class="content">ODC</span><span class="suffix"></span></h3>
<p>intune大礼包log。产品组喜欢用，跑命令安装脚本收日志</p>
<h3 id="syncml"><span class="prefix"></span><span class="content">syncml</span><span class="suffix"></span></h3>
<p>intune策略到设备上，设备接收到的信息，收到的value返回的value，return 200成功，500失败（需要sync）</p>
<h3 id="setup"><span class="prefix"></span><span class="content">setup</span><span class="suffix"></span></h3>
<p>所有windows层面的日志（Windows update、intune enroll、SCCM等log）</p>
<h3 id="remote-action"><span class="prefix"></span><span class="content">remote action</span><span class="suffix"></span></h3>
<p>remote action的整个流程：首先在portal端进行一个远程操作，然后这个操作会变成一条指令推送到设备上，设备收到了这条指令之后进行相应的操作。<br>
在portal上进行一个remote action操作之后，按F12会看到有一个API生成，向设备端发送远程指令。<br>
首先看portal上的指令到底有没有下发到设备上，可以在kusto中查看（凭借device ID， remote action 的内容还有操作的时间点来定位）<br>
如果说接收到了这样的一条指令但是仍然没有生效，那么就可以去收设备上的log，安卓debug log， iOS console log， Windows，可以看event viewer中的provider-admin，搜索远程操作的名称，或者在event viewer中查看PushNotification-platform</p>
<h3 id="mdm-clean-up"><span class="prefix"></span><span class="content">MDM clean up</span><span class="suffix"></span></h3>
<ul>
<li>在task scheduled中microsoft&gt;windows&gt;enterpriseMgmt，找到enrollment ID，删除enrollment ID 下所有的task，并且把这个enrollment ID 的文件夹删除</li>
<li>在registry editor中用enrollment ID搜索删除键值<br>
路径是HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Enrollments</li>
</ul>
<h3 id="consolex-log"><span class="prefix"></span><span class="content">Console&amp;x-log</span><span class="suffix"></span></h3>
<p>iOS设备，在无法收集company portal log时，可以连接数据线到mac上在mac上打开console，action中勾选收集log</p>
<h3 id="f12-log"><span class="prefix"></span><span class="content">F12 log</span><span class="suffix"></span></h3>
<p>intune portal上的报告或显示问题，尝试更换浏览器</p>
<h3 id="mhs（managed-home-screen）"><span class="prefix"></span><span class="content">MHS（managed home screen）</span><span class="suffix"></span></h3>
<p>android dedicated在展台模式下侧拉多次出现log</p>
<blockquote>
<p>Written with <a href="https://stackedit.cn/">StackEdit中文版</a>.</p>
</blockquote>

