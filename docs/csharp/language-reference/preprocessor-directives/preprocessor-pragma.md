---
title: '#pragma - C# リファレンス'
ms.date: 07/20/2015
f1_keywords:
- '#pragma'
helpviewer_keywords:
- '#pragma directive [C#]'
ms.assetid: 5b7944cd-d402-46a1-ad8f-feffb2d83673
ms.openlocfilehash: 3bd62364aeae0f21715711324655ef7d00d88afc
ms.sourcegitcommit: 5f236cd78cf09593c8945a7d753e0850e96a0b80
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75712456"
---
# <a name="pragma-c-reference"></a>#pragma (C# リファレンス)
`#pragma` は、ファイル内に指定され、そのファイルのコンパイルについての特別な命令をコンパイラに指示します。 命令はコンパイラによってサポートされている必要があります。 つまり、`#pragma` を使用してカスタムの前処理命令を作成することはできません。 Microsoft C# コンパイラは、次の 2 つの `#pragma` 命令をサポートしています。  
  
 [#pragma warning](./preprocessor-pragma-warning.md)  
  
 [#pragma checksum](./preprocessor-pragma-checksum.md)  
  
## <a name="syntax"></a>構文  
  
```csharp
#pragma pragma-name pragma-arguments  
```  
  
## <a name="parameters"></a>パラメーター  
 `pragma-name`  
 認識されているプラグマの名前。  
  
 `pragma-arguments`  
 プラグマに固有の引数。  
  
## <a name="see-also"></a>関連項目

- [C# リファレンス](../index.md)
- [C# プログラミング ガイド](../../programming-guide/index.md)
- [C# プリプロセッサ ディレクティブ](./index.md)
- [#pragma warning](./preprocessor-pragma-warning.md)
- [#pragma checksum](./preprocessor-pragma-checksum.md)
