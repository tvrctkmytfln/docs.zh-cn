---
ms.openlocfilehash: e128bdb5735b3e4844356ad4807cc092f6f2b105
ms.sourcegitcommit: 7476c20d2f911a834a00b8a7f5e8926bae6804d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88062415"
---
### <a name="no-aw-suffix-probing-on-non-windows-platforms"></a>不在非 Windows 平台上探测 A/W 后缀

在非 Windows 平台上探测 P/Invoke 期间，.NET 运行时不再向函数导出名称添加 `A` 或 `W` 后缀。

#### <a name="version-introduced"></a>引入的版本

5.0 预览版 4

#### <a name="change-description"></a>更改描述

[Windows 有一项约定](/windows/win32/intl/conventions-for-function-prototypes)，即要求向 Windows SDK 函数名称添加 `A` 或 `W` 后缀，这分别与 Windows 代码页面和 Unicode 版本相对应。

对于之前版本的 .NET，在导出对 P/Invoke 的发现结果的过程中，CoreCLR 和 Mono 运行时在所有平台上都会向导出名称添加 `A` 或 `W` 后缀。

而对于 .NET 5.0 及更高版本，在导出发现结果的过程中，仅在 Windows 上向导出名称添加 `A` 或 `W` 后缀。 在 Unix 平台上，不会添加后缀。 在 Windows 平台上，这两种运行时的语义保持不变。

#### <a name="reason-for-change"></a>更改原因

此项更改旨在简化跨平台探测。 如果非 Windows 平台不包含此语义，则不得产生此项开销。

#### <a name="recommended-action"></a>建议操作

若要缓解此更改的影响，可在非 Windows 平台上手动添加所需的后缀。 例如：

```csharp
[DllImport(...)]
extern static void SetWindowTextW();
```

#### <a name="category"></a>类别

Interop

#### <a name="affected-apis"></a>受影响的 API

- <xref:System.Runtime.InteropServices.DllImportAttribute?displayProperty=fullName>

<!--

#### Affected APIs

- `T:System.Runtime.InteropServices.DllImportAttribute`

-->
