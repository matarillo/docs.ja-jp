---
title: ICorProfilerInfo9::GetILToNativeMapping3
ms.date: 08/06/2019
dev_langs:
- cpp
api_name:
- ICorProfilerInfo9.GetILToNativeMapping3
api_location:
- mscorwks.dll
api_type:
- COM
author: davmason
ms.author: davmason
ms.openlocfilehash: 5c49d75432980d2f3af77ee040bc6eb20886b027
ms.sourcegitcommit: b11efd71c3d5ce3d9449c8d4345481b9f21392c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76861672"
---
# <a name="icorprofilerinfo9getiltonativemapping3-method"></a>ICorProfilerInfo9:: GetILToNativeMapping3 メソッド

ネイティブコードの開始アドレスが指定されている場合、この just-in-time バージョンのコードのネイティブから IL へのマッピング情報を返します。

## <a name="syntax"></a>構文

```cpp
HRESULT GetILToNativeMapping3( [in]  UINT_PTR pNativeCodeStartAddress,
                               [in]  ULONG32 cMap,
                               [out] ULONG32 *pcMap,
                               [out] COR_DEBUG_IL_TO_NATIVE_MAP map[]);
```

## <a name="parameters"></a>パラメーター

- `pNativeCodeStartAddress`

  \[] のネイティブ関数の先頭へのポインター。

- `cMap`

  \[] `map` 配列の最大サイズ。

- `pcMap`

  \[out] 使用可能な COR_DEBUG_IL_TO_NATIVE_MAP 構造の合計数。

- `map`

  \[out] [COR_DEBUG_IL_TO_NATIVE_MAP](../debugging/cor-debug-il-to-native-map-structure.md)構造体の配列。それぞれがオフセットを指定します。 `GetILToNativeMapping3` メソッドから制御が戻ると、`COR_DEBUG_IL_TO_NATIVE_MAP` 構造体の一部または全部が `map` に格納されます。

## <a name="remarks"></a>コメント

階層化コンパイルが有効になっている場合、メソッドは複数のネイティブコード本体を持つことができます。 [ICorProfilerInfo9:: GetNativeCodeStartAddresses](icorprofilerinfo9-getnativecodestartaddresses-method.md)は、すべてのネイティブコード本体の開始アドレスを返します。

## <a name="requirements"></a>要件

**プラットフォーム:** 「 [.Net Core でサポートされるオペレーティングシステム](../../../core/install/dependencies.md?tabs=netcore30&pivots=os-windows)」を参照してください。

**ヘッダー** : CorProf.idl、CorProf.h

**ライブラリ:** CorGuids.lib

**.NET Framework のバージョン:** [!INCLUDE[net_core_22](../../../../includes/net-core-22-md.md)]

## <a name="see-also"></a>関連項目

- [ICorProfilerInfo9 インターフェイス](icorprofilerinfo9-interface.md)
