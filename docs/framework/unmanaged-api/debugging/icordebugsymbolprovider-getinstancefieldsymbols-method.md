---
title: ICorDebugSymbolProvider::GetInstanceFieldSymbols 方法
ms.date: 03/30/2017
ms.assetid: a29b9233-ee67-4b53-b8bc-c00b281e7edb
ms.openlocfilehash: 9ecc61ed6cac73a519f33e00cbfbfecc20ac2ebe
ms.sourcegitcommit: d6bd7903d7d46698e9d89d3725f3bb4876891aa3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2020
ms.locfileid: "83379635"
---
# <a name="icordebugsymbolprovidergetinstancefieldsymbols-method"></a>ICorDebugSymbolProvider::GetInstanceFieldSymbols 方法
获取与 Typespec 签名相对应的实例字段符号。  
  
## <a name="syntax"></a>语法  
  
```cpp  
HRESULT GetInstanceFieldSymbols(  
   [in] ULONG32 cbSignature,  
   [in, size_is(cbSignature)]  BYTE typeSig[],  
   [in] ULONG32 cRequestedSymbols,  
   [out] ULONG32 *pcFetchedSymbols,  
   [out, size_is(cRequestedSymbols), length_is(*pcFetchedSymbols)] ICorDebugInstanceFieldSymbol *pSymbols[]  
);  
```  
  
## <a name="parameters"></a>参数  
 `cbSignature`  
 [in] `typeSig` 数组中的字节数。  
  
 `typeSig`  
 [in] 包含 `typespec` 签名的字节数组。  
  
 `cRequestedSymbols`  
 [in] 请求的符号数。  
  
 `pcFetchedSymbols`  
 [out] 一个指针，指向由方法检索的符号的数量。  
  
 `pSymbols`  
 弄指向[ICorDebugStaticFieldSymbol](icordebugstaticfieldsymbol-interface.md)数组的指针，该数组包含请求的实例字段符号。  
  
## <a name="remarks"></a>备注  
  
> [!NOTE]
> 此方法仅适用于 .NET Native。  
  
## <a name="requirements"></a>要求  
 **平台：** 请参阅[系统要求](../../get-started/system-requirements.md)。  
  
 **标头**：CorDebug.idl、CorDebug.h  
  
 **库：** CorGuids.lib  
  
 **.NET Framework 版本：**[!INCLUDE[net_46_native](../../../../includes/net-46-native-md.md)]  
  
## <a name="see-also"></a>请参阅

- [GetStaticFieldSymbols 方法](icordebugsymbolprovider-getstaticfieldsymbols-method.md)
- [ICorDebugSymbolProvider 接口](icordebugsymbolprovider-interface.md)
- [调试接口](debugging-interfaces.md)
