---
title: 列挙型デザイン
ms.date: 10/22/2008
ms.technology: dotnet-standard
helpviewer_keywords:
- type design guidelines, enumerations
- simple enumerations
- enumerations [.NET Framework], design guidelines
- class library design guidelines [.NET Framework], enumerations
- flags enumerations
ms.assetid: dd53c952-9d9a-4736-86ff-9540e815d545
ms.openlocfilehash: 3b24bfefd3edb0585e9c6369e9b8151b17151661
ms.sourcegitcommit: de17a7a0a37042f0d4406f5ae5393531caeb25ba
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76741716"
---
# <a name="enum-design"></a>列挙型デザイン

列挙型は、特別な種類の値型です。 列挙型には、単純な列挙型とフラグ列挙型の2種類があります。

単純な列挙型は、選択の小さな閉じられたセットを表します。 単純な列挙型の一般的な例としては、一連の色があります。

フラグ列挙型は、列挙値のビットごとの演算をサポートするように設計されています。 Flags 列挙型の一般的な例として、オプションの一覧があります。

厳密に型指定されたパラメーター、プロパティ、および値のセットを表す戻り値には、列挙型を使用✔️ます。

✔️は、静的な定数ではなく列挙型を使用するようにします。

オープンセット (オペレーティングシステムのバージョン、友人の名前など) には enum を使用しない ❌ ます。

❌ 将来の使用を目的とした予約済み列挙値を提供しません。

後の段階で、既存の列挙に値をいつでも追加できます。 列挙型に値を追加する方法の詳細については、「[列挙型への値の追加](#add_value)」を参照してください。 予約済みの値は、実際の値のセットを汚染するだけで、ユーザーエラーにつながる傾向があります。

❌ は、1つの値のみを持つ列挙型をパブリックに公開しないようにします。

C Api の将来の拡張性を確保するための一般的な方法は、メソッドシグネチャに予約済みのパラメーターを追加することです。 このような予約済みのパラメーターは、単一の既定値を持つ列挙型として表すことができます。 これは、マネージ Api では実行できません。 メソッドのオーバーロードでは、将来のリリースでパラメーターを追加できます。

❌ は、sentinel 値を列挙型に含めません。

Framework 開発者にとっては便利な場合もありますが、sentinel 値はフレームワークのユーザーにとって混乱を招くことがあります。 列挙型によって表されるセットの値の1つではなく、列挙型の状態を追跡するために使用されます。

単純な列挙型で値0を指定する✔️ます。

"None" のような値を呼び出すことを検討してください。 このような値がこの特定の列挙型に適していない場合は、列挙型の最も一般的な既定値に0の基になる値を代入する必要があります。

次のいずれかが当てはまる場合を除き、<xref:System.Int32> (ほとんどのプログラミング言語では既定) を列挙型の基になる型として使用することを✔️してください。

- 列挙型はフラグの列挙型であり、32を超えるフラグがあるか、または今後さらに多くなることが予想されます。

- 基になる型は、異なるサイズの列挙型を必要とするアンマネージコードとの相互運用性を容易にするために、<xref:System.Int32> とは異なる必要があります。

- 基になる型が小さいと、スペースが大幅に節約されます。 列挙型が主に制御フローの引数として使用されることが予想される場合、サイズの違いはほとんどありません。 次の場合、サイズを節約することが重要になります。

  - 非常に頻繁にインスタンス化される構造体またはクラスのフィールドとして列挙型を使用することを想定しています。

  - ユーザーは、大規模な配列または列挙型インスタンスのコレクションを作成することを想定しています。

  - 列挙型の多数のインスタンスをシリアル化することを想定しています。

メモリ内での使用については、マネージオブジェクトが常に `DWORD`に配置されていることに注意してください。したがって、インスタンス全体のサイズは常に `DWORD`に切り上げられるため、より小さな列挙型をパックするためには、インスタンス内に複数の列挙型またはその他の小さな構造体が効果的に必要です。

✔️は、複数形の名詞または名詞句を持つ列挙型と、単数形または名詞句を持つ単純な列挙型を使用します。

❌ <xref:System.Enum?displayProperty=nameWithType> を直接拡張しないでください。

<xref:System.Enum?displayProperty=nameWithType> は、ユーザー定義の列挙を作成するために CLR によって使用される特殊な型です。 ほとんどのプログラミング言語には、この機能にアクセスできるプログラミング要素が用意されています。 たとえば、 C#の `enum` キーワードを使用して、列挙型を定義します。

<a name="design"></a>

### <a name="designing-flag-enums"></a>フラグ列挙型のデザイン

✔️は、<xref:System.FlagsAttribute?displayProperty=nameWithType> を列挙型に適用します。 単純な列挙型にはこの属性を適用しないでください。

フラグの列挙値には2の累乗を使用して、ビットごとの OR 演算を使用して自由に組み合わせることができるように✔️ます。

一般的に使用されるフラグの組み合わせに対して特別な列挙値を指定することを✔️検討します。

ビットごとの演算は高度な概念であり、単純なタスクには必要ありません。 このような特殊な値の例としては、<xref:System.IO.FileAccess.ReadWrite> があります。

特定の値の組み合わせが無効な場合は、フラグの列挙型を作成しないように ❌ します。

次のガイドラインで規定されているように、値が "すべてのフラグがクリア" であり、適切な名前が付けられている場合を除き、フラグの列挙値0を使用しないように ❌ します。

✔️には、フラグの列挙型 `None`の0の値を指定します。 フラグの列挙型の場合、値は常に "すべてのフラグがクリアされている" ことを意味します。

<a name="add_value"></a>

### <a name="adding-value-to-enums"></a>列挙型への値の追加

列挙型に値を追加する必要があることを既に確認しています。 新しく追加された値が既存の API から返された場合、アプリケーションの互換性の問題が発生する可能性があります。これは、正しく記述されていないアプリケーションが新しい値を正しく処理できない可能性があるためです。

互換性のリスクが小さいとしても、列挙値に値を追加することを検討✔️。

列挙型への追加によって発生するアプリケーションの非互換性に関する実際のデータがある場合は、新しい値と古い値を返す新しい API を追加し、古い API を廃止することを検討してください。これにより、古い値だけが返されます。 これにより、既存のアプリケーションに互換性があることが保証されます。

*©2005、2009 Microsoft Corporation の部分。すべての権限が予約されています。*

*2008 年 10 月 22 日に Microsoft Windows Development シリーズの一部として、Addison-Wesley Professional によって発行された、Krzysztof Cwalina および Brad Abrams による「[Framework Design Guidelines: Conventions, Idioms, and Patterns for Reusable .NET Libraries, 2nd Edition](https://www.informit.com/store/framework-design-guidelines-conventions-idioms-and-9780321545619)」 (フレームワーク デザイン ガイドライン: 再利用可能な .NET ライブラリの規則、用法、パターン、第 2 版) から Pearson Education, Inc. の許可を得て再印刷されています。*

## <a name="see-also"></a>参照

- [型デザインのガイドライン](../../../docs/standard/design-guidelines/type.md)
- [フレームワーク デザインのガイドライン](../../../docs/standard/design-guidelines/index.md)
