<!---
    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.
-->

# org.apache.cordova.contacts

提供对设备的联系人数据库的访问。

**WARNING**： 联系人数据的收集和使用提出了重要的隐私问题。 您的应用程序的隐私策略应该讨论，应用程序如何使用联系人数据和它是否与任何其他的接口被共享。 联系信息被视为敏感，因为它揭示了人与人的沟通。 因此，除了应用程序的隐私策略，如果设备操作系统不会这样做了，您应该在应用程序访问或使用的联系人数据之前，强烈考虑提供-时间通知。 该通知应提供以上相同的信息，以及获取该用户的权限 （例如，通过**OK**和**No Thanks**提出的选择）。 请注意一些应用程序市场可能需要应用程序来提供时间的通知，并在访问联系人数据之前 获得相应用户的权限。 周围使用联系人数据的清晰和易于理解的用户体验有助于避免用户混淆和察觉联系人数据的误用。 有关详细信息，请参阅隐私指南。

## 安装

    cordova plugin add org.apache.cordova.contacts
    

### 火狐浏览器操作系统的怪癖

在[清单文件][1]中所述创建**www/manifest.webapp** 。 添加相关权限。 也是需要 web 应用程序的类型更改为"特权"-[清单文档][2]。 **警告**： 所有特权应用程序强制执行禁止内联脚本的[内容安全策略][3]。 在另一种方式初始化应用程序。

 [1]: https://developer.mozilla.org/en-US/Apps/Developing/Manifest
 [2]: https://developer.mozilla.org/en-US/Apps/Developing/Manifest#type
 [3]: https://developer.mozilla.org/en-US/Apps/CSP

    "type": "privileged",
    "permissions": {
        "contacts": {
            "access": "readwrite",
            "description": "Describe why there is a need for such permission"
        }
    }
    

### Windows 8 的怪癖

Windows 8 联系人是只读的。 透过科尔多瓦 API 接触不是可查询/搜索，你应通知用户挑选联系人为 contacts.pickContact，将打开 '人民' 应用程序调用，用户必须选择一个联系人。 返回任何联系人均为只读，因此您的应用程序不能修改它们。

## navigator.contacts

### 方法

*   navigator.contacts.create
*   navigator.contacts.find
*   navigator.contacts.pickContact

### 对象

*   Contact
*   ContactName
*   ContactField
*   ContactAddress
*   ContactOrganization
*   ContactFindOptions
*   ContactError
*   ContactFieldType

## navigator.contacts.create

`navigator.contacts.create`方法是同步的并返回一个新的 `Contact` 对象。

此方法不会保留联系人对象在设备的联系人数据库中，您需要为其调用 `Contact.save` 方法。

### 支持的平台

*   Android 系统
*   黑莓 10
*   火狐浏览器操作系统
*   iOS
*   Windows Phone 7 和 8

### 示例

    var myContact = navigator.contacts.create({"displayName": "Test User"});
    

## navigator.contacts.find

`navigator.contacts.find`方法以异步方式，执行设备的联系人数据库查询并返回一个数组的 `Contact` 对象。 所得到的对象传递给 `contactSuccess` **contactSuccess**参数所指定的回调函数。

**ContactFields**参数指定的字段用作搜索的限定符。 零长度**contactFields**参数是无效的结果在 `ContactError.INVALID_ARGUMENT_ERROR` 。 **ContactFields**值为 `"*"` 返回所有联系人字段。

**ContactFindOptions.filter**字符串查询联系人数据库时，可以用作搜索筛选器。 如果提供，不区分大小写，部分值匹配应用于在**contactFields**参数中指定的每个字段。 如果有*任何*指定的字段的匹配，则返回该联系人。 使用**contactFindOptions.desiredFields**参数来控制哪些联系人属性必须回返回。

### 参数

*   **contactSuccess**： 从数据库返回成功回调函数调用时使用的联系人对象的数组。[要求]

*   **contactError**： 错误回调函数，当发生错误时调用。[可选]

*   **contactFields**： 联系人字段使用作为搜索的限定符。*(DOMString[])*[要求]

*   **contactFindOptions**: 搜索选项来筛选 navigator.contacts。[可选]键包括：

*   **筛选器**： 用来查找 navigator.contacts 的搜索字符串。*() DOMString*（默认值：`""`)

*   **多个**： 确定是否查找操作返回多个 navigator.contacts。*（布尔）*（默认值：`false`)
    
    *   **desiredFields**: 联系要回返回的字段。如果指定了，生成的 `Contact` 对象只能使用这些字段的值。*(DOMString[])*[可选]

### 支持的平台

*   Android 系统
*   黑莓 10
*   火狐浏览器操作系统
*   iOS
*   Windows Phone 7 和 8

### 示例

    function onSuccess(contacts) {
        alert('Found ' + contacts.length + ' contacts.');
    };
    
    function onError(contactError) {
        alert('onError!');
    };
    
    // find all contacts with 'Bob' in any name field
    var options      = new ContactFindOptions();
    options.filter   = "Bob";
    options.multiple = true;
    options.desiredFields = [navigator.contacts.fieldType.id];
    var fields       = [navigator.contacts.fieldType.displayName, navigator.contacts.fieldType.name];
    navigator.contacts.find(onSuccess, onError, fields, options);
    

## navigator.contacts.pickContact

`navigator.contacts.pickContact`方法启动联系人选择器来选择一个联系人。 所产生的对象传递给 `contactSuccess` **contactSuccess**参数所指定的回调函数。

### 参数

*   **contactSuccess**: 成功使用单个联系人对象调用的回调函数。[要求]

*   **contactError**： 错误回调函数，当发生错误时调用。[可选]

### 支持的平台

*   Android 系统
*   iOS
*   Windows Phone 8
*   Windows 8

### 示例

    navigator.contacts.pickContact(function(contact){
            console.log('The following contact has been selected:' + JSON.stringify(contact));
        },function(err){
            console.log('Error: ' + err);
        });
    

## 联系人

`Contact`对象表示用户的联系人。 可以创建、 存储，或从设备的联系人数据库中删除的联系人。 联系人可以还 (单独或批量） 从数据库中检索通过调用 `navigator.contacts.find` 方法。

**注**： 以上列出的联系人字段并不是所有平台支持的每个设备。请检查每个平台*的怪癖*节了解详细信息。

### 属性

*   **id**： 一个全局唯一标识符。*() DOMString*

*   **显示名称**： 此联系人，适合于向最终用户显示的名称。*() DOMString*

*   **名称**： 包含所有组件的人的名称的对象。*(联系人姓名)*

*   **昵称**: 休闲的解决联系人名称。*() DOMString*

*   **手机**： 一个数组的所有联系人的电话号码。*(ContactField[])*

*   **电子邮件**： 所有联系人的电子邮件地址的数组。*(ContactField[])*

*   **地址**： 该联系人的所有地址的数组。*(ContactAddress[])*

*   **ims**： 所有联系人的 IM 地址的数组。*(ContactField[])*

*   **组织**： 该联系人的所有组织的数组。*(ContactOrganization[])*

*   **生日**： 联系人的生日。*（日期）*

*   **注**： 有关联系人的注释。*() DOMString*

*   **照片**： 数组联系人的照片。*(ContactField[])*

*   **类别**： 数组与联系人关联的所有用户定义的类别。*(ContactField[])*

*   **url**： 数组与联系人关联的 web 页。*(ContactField[])*

### 方法

*   **克隆**： 返回一个新的 `Contact` 对象就是调用对象的深层副本 `id` 属性设置为`null`.

*   **删除**： 从设备的联系人数据库中删除联系人，否则执行错误回调与 `ContactError` 对象。

*   **保存**： 将一个新的联系人保存到设备的联系人数据库中，或更新现有的联系人 （如果已存在具有相同**id**的联系人。

### 支持的平台

*   亚马逊火 OS
*   Android 系统
*   黑莓 10
*   火狐浏览器操作系统
*   iOS
*   Windows Phone 7 和 8
*   Windows 8

### 保存示例

    function onSuccess(contact) {
        alert("Save Success");
    };
    
    function onError(contactError) {
        alert("Error = " + contactError.code);
    };
    
    // create a new contact object
    var contact = navigator.contacts.create();
    contact.displayName = "Plumber";
    contact.nickname = "Plumber";            // specify both to support all devices
    
    // populate some fields
    var name = new ContactName();
    name.givenName = "Jane";
    name.familyName = "Doe";
    contact.name = name;
    
    // save to device
    contact.save(onSuccess,onError);
    

### 克隆示例

        // clone the contact object
        var clone = contact.clone();
        clone.name.givenName = "John";
        console.log("Original contact name = " + contact.name.givenName);
        console.log("Cloned contact name = " + clone.name.givenName);
    

### 删除示例

    function onSuccess() {
        alert("Removal Success");
    };
    
    function onError(contactError) {
        alert("Error = " + contactError.code);
    };
    
    // remove the contact from the device
    contact.remove(onSuccess,onError);
    

### Android 2.X 的怪癖

*   **类别**： 不支持 Android 2.X 的设备上，返回`null`.

### 黑莓 10 怪癖

*   **id**： 由该设备分配时保存该联系人。

### FirefoxOS 的怪癖

*   **类别**： 部分支持。返回字段**治安**和**类型**`null`

*   **ims**： 不支持

*   **照片**： 不支持

### iOS 的怪癖

*   **显示名称**： 上返回的 iOS 不支持 `null` 除非有没有 `ContactName` 指定，在这种情况下它将返回复合名称，**绰号**或 `""` ，分别。

*   **生日**： 必须输入 JavaScript 作为 `Date` 对象，它将返回相同的方式。

*   **照片**： 返回到图像，存储在应用程序的临时目录中的文件的 URL。当应用程序退出时删除临时目录的内容。

*   **类别**： 这目前不支持属性，返回`null`.

### Windows Phone 7 和 8 怪癖

*   **显示名称**： 当创建一个联系人，提供的显示名称参数不同于显示名称的值检索查找联系人时。

*   **url**： 当创建一个联系人，用户可以输入和保存多个 web 地址，但只有一个是可用的搜索联系人时。

*   **手机**： 不支持*参照*选项。 在*查找*操作中不是支持的*类型*。 只有一个 `phoneNumber` 允许为每个*类型*.

*   **电子邮件**：*参照*选项不受支持。家庭和个人使用引用同一电子邮件项。只有一项被允许为每个*类型*.

*   **地址**： 仅支持工作和家庭/个人*类型*。家庭和个人*类型*引用相同的地址条目。只有一项被允许为每个*类型*.

*   **组织**： 唯一一个允许的并且不支持的*治安*、*类型*和*新闻部*的属性。

*   **注意**： 不支持返回`null`.

*   **ims**: 不受支持，返回`null`.

*   **生日**: 不受支持，返回`null`.

*   **类别**: 不受支持，返回`null`.

## ContactAddress

`ContactAddress`对象存储的单一地址的联系人的属性。 A `Contact` 对象可能包括多个地址在 `ContactAddress[]` 数组。

### 属性

*   **上一页**: 设置为 `true` 如果此 `ContactAddress` 包含用户的首选的值。*（布尔）*

*   **类型**： 一个字符串，例如指示哪种类型的字段这是*回家*。*() DOMString*

*   **格式**： 显示格式的完整地址。*() DOMString*

*   **streetAddress**： 完整的街道地址。*() DOMString*

*   **地点**： 城市或地点。*() DOMString*

*   **区域**： 国家或地区。*() DOMString*

*   **邮政编码**： 邮政编码或邮政代码。*() DOMString*

*   **国家**： 国家名称。*() DOMString*

### 支持的平台

*   亚马逊火 OS
*   Android 系统
*   黑莓 10
*   火狐浏览器操作系统
*   iOS
*   Windows Phone 7 和 8
*   Windows 8

### 示例

    // display the address information for all contacts
    
    function onSuccess(contacts) {
        for (var i = 0; i < contacts.length; i++) {
            for (var j = 0; j < contacts[i].addresses.length; j++) {
                alert("Pref: "         + contacts[i].addresses[j].pref          + "\n" +
                    "Type: "           + contacts[i].addresses[j].type          + "\n" +
                    "Formatted: "      + contacts[i].addresses[j].formatted     + "\n" +
                    "Street Address: " + contacts[i].addresses[j].streetAddress + "\n" +
                    "Locality: "       + contacts[i].addresses[j].locality      + "\n" +
                    "Region: "         + contacts[i].addresses[j].region        + "\n" +
                    "Postal Code: "    + contacts[i].addresses[j].postalCode    + "\n" +
                    "Country: "        + contacts[i].addresses[j].country);
            }
        }
    };
    
    function onError(contactError) {
        alert('onError!');
    };
    
    // find all contacts
    var options = new ContactFindOptions();
    options.filter = "";
    var filter = ["displayName", "addresses"];
    navigator.contacts.find(filter, onSuccess, onError, options);
    

### Android 2.X 的怪癖

*   **上一页**: 不受支持，返回 `false` Android 2.X 的设备上。

### 黑莓 10 怪癖

*   **上一页**： 返回的黑莓设备上不支持`false`.

*   **类型**： 部分支持。*工作*和*家庭*类型地址的每个唯一一个可以存储每个联系人。

*   **格式化**： 部分支持。返回的串联的所有黑莓手机地址字段。

*   **streetAddress**: 支持。返回的串联的黑莓**地址 1**和**地址 2**的地址字段。

*   **现场**: 支持。黑莓手机**城**地址字段中存储。

*   **区域**： 支持。黑莓手机**stateProvince**地址字段中存储。

*   **邮政编码**: 支持。黑莓手机**zipPostal**地址字段中存储。

*   **国家**： 支持。

### FirefoxOS 的怪癖

*   **格式化**： 目前不支持

### iOS 的怪癖

*   **上一页**： 返回的 iOS 设备上不支持`false`.

*   **格式化**： 目前不支持。

### Windows 8 的怪癖

*   **上一页**： 不支持

## ContactError

`ContactError`对象返回到用户通过 `contactError` 回调函数时出现错误。

### 属性

*   **代码**： 下面列出的预定义的错误代码之一。

### 常量

*   `ContactError.UNKNOWN_ERROR` (code 0)
*   `ContactError.INVALID_ARGUMENT_ERROR` (code 1)
*   `ContactError.TIMEOUT_ERROR` (code 2)
*   `ContactError.PENDING_OPERATION_ERROR` (code 3)
*   `ContactError.IO_ERROR` (code 4)
*   `ContactError.NOT_SUPPORTED_ERROR` (code 5)
*   `ContactError.PERMISSION_DENIED_ERROR` (code 20)

## ContactField

`ContactField`对象是可重用的组件表示泛指联系人字段。 每个 `ContactField` 对象包含 `value` ， `type` ，和 `pref` 属性。 A `Contact` 对象存储中的几个属性 `ContactField[]` 数组，如电话号码和电子邮件地址。

在大多数情况下，没有任何预先确定的值为 `ContactField` 对象的**type**属性。 例如，电话号码可以指定**类型**的*家庭*、*工作*、*手机*、 *iPhone*或由一个特定的设备平台联系数据库支持的任何其他值的值。 然而，对于 `Contact` **的照片**字段，**类型**字段指示返回图像的格式： **url**的**值**属性包含的照片图像或*base64*的 URL 时的**值**包含一个 base64 编码的图像字符串时。

### 属性

*   **类型**： 一个字符串，例如指示哪种类型的字段这是*回家*。*() DOMString*

*   **值**： 字段的值，如电话号码或电子邮件地址。*() DOMString*

*   **上一页**: 设置为 `true` 如果此 `ContactField` 包含用户的首选的值。*（布尔）*

### 支持的平台

*   亚马逊火 OS
*   Android 系统
*   黑莓 10
*   火狐浏览器操作系统
*   iOS
*   Windows Phone 7 和 8
*   Windows 8

### 示例

        // create a new contact
        var contact = navigator.contacts.create();
    
        // store contact phone numbers in ContactField[]
        var phoneNumbers = [];
        phoneNumbers[0] = new ContactField('work', '212-555-1234', false);
        phoneNumbers[1] = new ContactField('mobile', '917-555-5432', true); // preferred number
        phoneNumbers[2] = new ContactField('home', '203-555-7890', false);
        contact.phoneNumbers = phoneNumbers;
    
        // save the contact
        contact.save();
    

### Android 的怪癖

*   **上一页**: 不受支持，返回`false`.

### 黑莓 10 怪癖

*   **类型**： 部分支持。使用的电话号码。

*   **值**： 支持。

*   **上一页**: 不受支持，返回`false`.

### iOS 的怪癖

*   **上一页**: 不受支持，返回`false`.

### Windows8 的怪癖

*   **上一页**: 不受支持，返回`false`.

## 联系人姓名

包含有关的不同种类的信息 `Contact` 对象的名称。

### 属性

*   **格式化**： 该联系人的完整名称。*() DOMString*

*   **家族**： 联系人的姓氏。*() DOMString*

*   **givenName**： 联系人的名字。*() DOMString*

*   **之间**： 联系人的中间名。*() DOMString*

*   **honorificPrefix**： 联系人的前缀 (例如*先生*或*博士*） *(DOMString)*

*   **honorificSuffix**： 联系人的后缀 （如*律师*）。*() DOMString*

### 支持的平台

*   亚马逊火 OS
*   Android 2.X
*   黑莓 10
*   火狐浏览器操作系统
*   iOS
*   Windows Phone 7 和 8
*   Windows 8

### 示例

    function onSuccess(contacts) {
        for (var i = 0; i < contacts.length; i++) {
            alert("Formatted: "  + contacts[i].name.formatted       + "\n" +
                "Family Name: "  + contacts[i].name.familyName      + "\n" +
                "Given Name: "   + contacts[i].name.givenName       + "\n" +
                "Middle Name: "  + contacts[i].name.middleName      + "\n" +
                "Suffix: "       + contacts[i].name.honorificSuffix + "\n" +
                "Prefix: "       + contacts[i].name.honorificSuffix);
        }
    };
    
    function onError(contactError) {
        alert('onError!');
    };
    
    var options = new ContactFindOptions();
    options.filter = "";
    filter = ["displayName", "name"];
    navigator.contacts.find(filter, onSuccess, onError, options);
    

### Android 的怪癖

*   **格式化**： 部分支持，和只读。 返回的串联的 `honorificPrefix` ， `givenName` ， `middleName` ， `familyName` ，和`honorificSuffix`.

### 黑莓 10 怪癖

*   **格式化**： 部分支持。返回的串联的黑莓手机**名字**和**姓氏**字段。

*   **家族**: 支持。黑莓**姓氏**字段中存储。

*   **givenName**： 支持。黑莓**名字**字段中存储。

*   **之间**： 不受支持，返回`null`.

*   **honorificPrefix**: 不受支持，返回`null`.

*   **honorificSuffix**: 不受支持，返回`null`.

### FirefoxOS 的怪癖

*   **格式化**： 部分支持，和只读。 返回的串联的 `honorificPrefix` ， `givenName` ， `middleName` ， `familyName` ，和`honorificSuffix`.

### iOS 的怪癖

*   **格式化**： 部分支持。返回 iOS 复合名称，但为只读。

### Windows 8 的怪癖

*   **格式**： 这是唯一名称属性，并且是相同的 `displayName` ，和`nickname`

*   **家族**: 不支持

*   **givenName**: 不支持

*   **之间**： 不支持

*   **honorificPrefix**: 不支持

*   **honorificSuffix**: 不支持

## ContactOrganization

`ContactOrganization`对象存储的联系人的组织属性。A `Contact` 对象存储一个或多个 `ContactOrganization` 数组中的对象。

### 属性

*   **上一页**: 设置为 `true` 如果此 `ContactOrganization` 包含用户的首选的值。*（布尔）*

*   **类型**： 一个字符串，例如指示哪种类型的字段这是*回家*。_(DOMString)

*   **名称**： 组织的名称。*() DOMString*

*   **部门**： 新闻部的工程合约。*() DOMString*

*   **标题**： 在组织联系人的标题。*() DOMString*

### 支持的平台

*   Android 系统
*   黑莓 10
*   火狐浏览器操作系统
*   iOS
*   Windows Phone 7 和 8

### 示例

    function onSuccess(contacts) {
        for (var i = 0; i < contacts.length; i++) {
            for (var j = 0; j < contacts[i].organizations.length; j++) {
                alert("Pref: "      + contacts[i].organizations[j].pref       + "\n" +
                    "Type: "        + contacts[i].organizations[j].type       + "\n" +
                    "Name: "        + contacts[i].organizations[j].name       + "\n" +
                    "Department: "  + contacts[i].organizations[j].department + "\n" +
                    "Title: "       + contacts[i].organizations[j].title);
            }
        }
    };
    
    function onError(contactError) {
        alert('onError!');
    };
    
    var options = new ContactFindOptions();
    options.filter = "";
    filter = ["displayName", "organizations"];
    navigator.contacts.find(filter, onSuccess, onError, options);
    

### Android 2.X 的怪癖

*   **上一页**: 不支持的 Android 2.X 的设备，返回`false`.

### 黑莓 10 怪癖

*   **上一页**: 不支持的黑莓设备，返回`false`.

*   **类型**： 不支持的黑莓设备，返回`null`.

*   **名称**： 部分支持。第一次组织名称存储在黑莓**公司**字段中。

*   **部**: 不受支持，返回`null`.

*   **标题**: 部分支持。第一次组织标题是黑莓**jobTitle**字段中存储的。

### 火狐浏览器操作系统的怪癖

*   **上一页**： 不支持

*   **类型**： 不支持

*   **新闻部**： 不支持

*   字段**名称**和**标题**存储在**组织结构图**和**jobTitle**.

### iOS 的怪癖

*   **上一页**： 返回的 iOS 设备上不支持`false`.

*   **类型**： 返回的 iOS 设备上不支持`null`.

*   **名称**： 部分支持。第一次组织名称存储在 iOS **kABPersonOrganizationProperty**字段中。

*   **新闻部**： 部分支持。第一部名称存储在 iOS **kABPersonDepartmentProperty**字段中。

*   **标题**: 部分支持。第一个标题是 iOS **kABPersonJobTitleProperty**字段中存储的。