---
title: F#コンポーネントのデザイン ガイドライン
description: 他の呼び出し元が使用する F# コンポーネントを記述するためのガイドラインについて説明します。
ms.date: 05/14/2018
ms.openlocfilehash: c61e4cd9098388b356c71c325d66c760fa866cf0
ms.sourcegitcommit: 9b552addadfb57fab0b9e7852ed4f1f1b8a42f8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "61902162"
---
# <a name="f-component-design-guidelines"></a>F#コンポーネントのデザイン ガイドライン

このドキュメントは、Microsoft Research のF# コンポーネント デザイン ガイドライン v14、および、当初 F# Software Foundation によって収集され管理されている[別バージョン](https://fsharp.org/specs/component-design-guidelines/) に基づいた、F# プログラミングのコンポーネント デザイン ガイドラインの集合です。

このドキュメントでは、読者が F# プログラミングに慣れていることを前提としています。 このガイドのさまざまなバージョンに対する貢献と役立つフィードバックについて、F# コミュニティに感謝します。

## <a name="overview"></a>概要

このドキュメントは、F# コンポーネント デザインおよびコーディングに関連する問題の一部を考察します。 コンポーネントは、次のいずれかの意味を持ちます。

* そのプロジェクト内の外部のコンシューマーを持つ F# プロジェクトのレイヤー。
* アセンブリ境界を越えて F# コードで使用されるためのライブラリ。
* アセンブリ境界を越えて任意の .NET 言語で使用されるためのライブラリ。
* [NuGet](https://nuget.org)などのパッケージ リポジトリを使用して配布するためのライブラリ。

この記事で説明する手法は、[優れた F# コードの 5 つの原則](index.md#five-principles-of-good-f-code)に従って、関数型プログラミングとオブジェクトプログラミングの両方を適切に利用します。

方法論に関係なく、コンポーネントとライブラリのデザイナーは、開発者が最も簡単に使用できる API を作成しようとするとき、多くの実用的かつ散発的な問題に直面します。 [.NET ライブラリのデザイン ガイドライン](../../standard/design-guidelines/index.md) の良心的な適用は、快適に使用できる一貫したAPI集合を作成するよう導くでしょう。

## <a name="general-guidelines"></a>一般的なガイドライン

適用されるいくつかのユニバーサル ガイドラインがあるF#ライブラリ、ライブラリの対象となるユーザーに関係なく。

### <a name="learn-the-net-library-design-guidelines"></a>.NET ライブラリのデザイン ガイドラインについて説明します

種類に関係なくのF#コーディングを行うの実務知識を持ちすることが重要、 [.NET ライブラリのデザイン ガイドライン](../../standard/design-guidelines/index.md)します。 ほとんどの他の F# および .NET プログラマは、次のガイドラインに習熟しに準拠するように .NET コードを期待します。

.NET ライブラリのデザイン ガイドラインは、名前付け、クラス、インターフェイス、メンバーのデザイン (プロパティ、メソッド、イベントなど) およびの詳細は、設計に関する一般的なガイダンスを提供し、さまざまな設計ガイダンスの参照の最初のポイントを便利なはします。

### <a name="add-xml-documentation-comments-to-your-code"></a>XML ドキュメントのコメントをコードに追加します。

パブリック Api での XML ドキュメントでは、ライブラリのファイルをこれらの型とメンバー、および有効にするドキュメントの構築に使用するときに、優れたの Intellisense とクイック ヒントを取得できることを確認します。 参照してください、 [XML ドキュメント](../language-reference/xml-documentation.md)内で、xmldoc コメントの追加のマークアップを使用できるさまざまな xml タグの概要。

```fsharp
/// A class for representing (x,y) coordinates
type Point =

    /// Computes the distance between this point and another
    member DistanceTo: otherPoint:Point -> float
```

短い形式のいずれかの XML コメントを使用することができます (`/// comment`)、または標準の XML コメント (`///<summary>comment</summary>`)。

### <a name="consider-using-explicit-signature-files-fsi-for-stable-library-and-component-apis"></a>安定したライブラリと Api のコンポーネントの明示的な署名ファイル (.fsi) の使用を検討してください。

明示的な署名ファイルを使用して、F#ライブラリは、どちらも、ライブラリのサーフェスのパブリック ドキュメント間を明確に分離を提供し、内部の完全な公開がわかっていることを確認するのには、パブリック API の簡潔な概要を提供します。実装の詳細。 シグネチャ ファイルが実装と署名の両方のファイルに対する変更を要求することによって、パブリック API を変更する際の問題を追加することに注意してください。 その結果、署名ファイルを通常のみ際に挿入される API 確固たるものになりますが、大幅に変更される予定ですされなくします。

### <a name="always-follow-best-practices-for-using-strings-in-net"></a>常に .NET で文字列を使用するためのベスト プラクティスに従ってください。

次の[.NET で文字列を使用するためのベスト プラクティス](../../standard/base-types/best-practices-strings.md)ガイダンス。 具体的には、常に明示的に状態*文化的なインテント*変換と文字列の比較 (該当する場合)。

## <a name="guidelines-for-f-facing-libraries"></a>に関するガイドラインF#-ライブラリに接続します。

このセクションでは、パブリック F# を開発するための推奨事項を表示します-ライブラリ; に接続します。F# 開発者が使用するためのパブリック Api を公開するライブラリは、します。 具体的には適用可能なさまざまなライブラリ デザインの推奨事項がありますF#します。 特定の推奨事項がない場合、.NET ライブラリのデザイン ガイドラインは、フォールバックのガイダンスです。

### <a name="naming-conventions"></a>名前付け規則

#### <a name="use-net-naming-and-capitalization-conventions"></a>.NET の名前付けおよび大文字と小文字の規則を使用します。

次の表では、.NET の名前付けおよび大文字と小文字の規則に従います。 あることが小さな追加F#を構築します。

| 構成体 | Case | パーツ | 使用例 | メモ |
|-----------|------|------|----------|-------|
| 具象型 | PascalCase | 名詞または形容詞 | 一覧で、Double、複雑な | 具象型は、構造体、クラス、列挙、デリゲート、レコード、および共用体です。 型名が小文字従来 OCaml には、F# 型の .NET の名前付けスキームは採用します。
| DLL           | PascalCase |                 | Fabrikam.Core.dll |  |
| 共用体タグ     | PascalCase | 名詞 | いくつかの追加、成功 | パブリック Api では、プレフィックスを使用しないでください。 必要に応じてときなど、内部プレフィックスを使用します。 `type Teams = TAlpha | TBeta | TDelta.` |
| event          | PascalCase | 動詞 | ValueChanged/ValueChanging |  |
| 例外     | PascalCase |      | WebException | 名前は、"Exception"で終わる必要があります。 |
| フィールド          | PascalCase | 名詞 | CurrentName  | |
| インターフェイス型 |  PascalCase | 名詞または形容詞 | IDisposable | 名前は、"I"で始まる必要があります。 |
| メソッド |  PascalCase |  動詞 | ToString | |
| 名前空間 | PascalCase | | Microsoft.FSharp.Core | 一般的に使用して`<Organization>.<Technology>[.<Subnamespace>]`、ただし、テクノロジは、組織の独立した場合、組織を削除します。 |
| パラメーター | キャメル ケース | 名詞 |  typeName、変換、範囲 | |
| (内部) の値を使用します。 | キャメル ケースまたは PascalCase | 名詞と動詞 |  getValue、myTable |
| 値 (外部) の使用します。 | キャメル ケースまたは PascalCase | 名詞と動詞  | List.map、Dates.Today | let バインドされた値は、従来の機能の設計パターンに従うときに多くの場合、パブリック。 ただし、一般に PascalCase とき使用識別子は、他の .NET 言語から使用できます。 |
| プロパティ  | PascalCase  | 名詞または形容詞  | IsEndOfFile、背景色  | ブール型プロパティ一般的に使用されできます IsEndOfFile、IsNotEndOfFile しないように、肯定を指定する必要があります。

#### <a name="avoid-abbreviations"></a>省略形を回避します。

.NET のガイドラインの省略形の使用を防ぐため (たとえば、"を使用して、`OnButtonClick`なく`OnBtnClick`")。 共通の省略形など`Async`「非同期」の場合は許容されます。 このガイドラインは関数型プログラミング; も無視されます。たとえば、 `List.iter` 「反復処理する」の省略形を使用します。 このため、略語を使用して F# のより充実を許容する傾向があります-に-F# プログラミングでは、まだ一般にパブリック コンポーネントのデザインに避ける必要がありますが、します。

#### <a name="avoid-casing-name-collisions"></a>大文字小文字の区別、名前の衝突を避ける

.NET のガイドラインには、一部のクライアント言語 (Visual Basic など) は大文字であるために、名前の競合を解消するために単独で大文字小文字の区別を使用できないことがあるとします。

#### <a name="use-acronyms-where-appropriate"></a>適切な場所、頭字語を使用します。

XML などの頭字語は、省略形でないや、uncapitalized 形式 (Xml) での .NET ライブラリで広く使用されます。 のみ広く認識させ、よく知られた頭字語を使用する必要があります。

#### <a name="use-pascalcase-for-generic-parameter-names"></a>PascalCase を使用して、ジェネリック パラメーター名

などのパブリック Api でのジェネリック パラメーターの名前を PascalCase を使用しないでくださいF#-ライブラリに接続します。 ような名前を使用して、具体的には、 `T`、 `U`、 `T1`、`T2`の任意のジェネリック パラメーターを使用して特定の名前は合理的で、し、F# のライブラリに接続するような名前を使用して、 `Key`、 `Value`、 `Arg`(ただし、たとえばいない`TKey`)。

#### <a name="use-either-pascalcase-or-camelcase-for-public-functions-and-values-in-f-modules"></a>PascalCase またはキャメル ケースのいずれかを使用して、パブリック関数および F# モジュール内の値

キャメル ケースが使用するように設計されたパブリック関数の使用非修飾 (たとえば、 `invalidArg`)、および (List.map など) の「標準的なコレクション関数」。 どちらの場合も、関数名は言語のキーワードのようにずっと機能します。

### <a name="object-type-and-module-design"></a>オブジェクト、型、およびモジュールの設計

#### <a name="use-namespaces-or-modules-to-contain-your-types-and-modules"></a>名前空間またはモジュールを使用して、型とモジュール

コンポーネントでそれぞれの F# ファイルの名前空間の宣言またはモジュールの宣言のいずれかで開始します。

```fsharp
namespace Fabrikam.BasicOperationsAndTypes

type ObjectType1() =
    ...

type ObjectType2() =
     ...

module CommonOperations =
    ...
```

または

```fsharp
module Fabrikam.BasicOperationsAndTypes

type ObjectType1() =
    ...

type ObjectType2() =
    ...

module CommonOperations =
    ...
```

モジュールや名前空間を使用して、最上位レベルのコードを整理する間の相違点は次のとおりです。

* 名前空間が複数のファイルにまたがることができます。
* 内部のモジュール内である場合を除いて、名前空間は F# の関数を含めることはできません。
* 単一ファイル内の特定のモジュールのコードを含める必要があります。
* 最上位レベルのモジュールが内部のモジュールを必要としないの F# 関数を含めることができます。

最上位レベルの名前空間またはモジュールの間で choice では、コードのコンパイル済みの形式に影響し、したがってが影響他の .NET 言語で表示する必要があります API 最終的に使用する F# コードの外部に。

#### <a name="use-methods-and-properties-for-operations-intrinsic-to-object-types"></a>メソッドとプロパティを使用して、オブジェクトの種類に固有の操作

オブジェクトを使用する場合は、メソッドとプロパティをその型として使用できる機能が実装されることを確認することをお勧めします。

```fsharp
type HardwareDevice() =

    member this.ID = ...

    member this.SupportedProtocols = ...

type HashTable<'Key,'Value>(comparer: IEqualityComparer<'Key>) =

    member this.Add(key, value) = ...

    member this.ContainsKey(key) = ...

    member this.ContainsValue(value) = ...
```

必要があります、必ずしも、メンバーに実装していないメンバーについては、機能の大部分が機能を使用できる部分にする必要があります。

#### <a name="use-classes-to-encapsulate-mutable-state"></a>クラスを使用して、変更可能な状態をカプセル化

F#、これだけ行う必要がある場所、状態は既にカプセル化されていないクロージャ、シーケンス式、または非同期計算などの別の言語コンストラクトです。

```fsharp
type Counter() =
    // let-bound values are private in classes.
    let mutable count = 0

    member this.Next() =
        count <- count + 1
        count
```

#### <a name="use-interfaces-to-group-related-operations"></a>インターフェイスを使用してグループ化に関連する操作

インターフェイスの種類を使用して、一連の操作を表します。 関数の組などの関数のレコードの他のオプションをお勧めします。

```fsharp
type Serializer =
    abstract Serialize<'T>: preserveRefEq: bool -> value: 'T -> string
    abstract Deserialize<'T>: preserveRefEq: bool -> pickle: string -> 'T
```

優先。

```fsharp
type Serializer<'T> = {
    Serialize: bool -> 'T -> string
    Deserialize: bool -> string -> 'T
}
```

インターフェイスは、.net では、何ファンクターは通常表示を実現するために使用できる最上級の概念です。 さらに、関数のレコードのことはできませんが、プログラムに移った型のエンコードに使用できます。

#### <a name="use-a-module-to-group-functions-which-act-on-collections"></a>コレクションの操作を実行するグループの機能をモジュールを使用します。

コレクション型を定義するときになどの操作の標準セットを提供することを検討してください`CollectionType.map`と`CollectionType.iter`) の新しいコレクション型。

```fsharp
module CollectionType =
    let map f c =
        ...
    let iter f c =
        ...
```

このようなモジュールを含める場合は FSharp.Core の関数の名前付け規則に従います。

#### <a name="use-a-module-to-group-functions-for-common-canonical-functions-especially-in-math-and-dsl-libraries"></a>特にで数値演算と DSL ライブラリの一般的な正規の関数グループの機能をモジュールを使用します。

たとえば、`Microsoft.FSharp.Core.Operators`は、自動的に開かれているコレクションの最上位の関数です (など`abs`と`sin`) FSharp.Core.dll によって提供されます。

同様に、統計ライブラリが関数を持つモジュールを含めることがあります`erf`と`erfc`を明示的にまたは自動的に開くこのモジュールは設計されています。

#### <a name="consider-using-requirequalifiedaccess-and-carefully-apply-autoopen-attributes"></a>RequireQualifiedAccess の使用を検討し、慎重に AutoOpen 属性を適用

追加、`[<RequireQualifiedAccess>]`属性をモジュールには、モジュールが開かれていないことと、アクセスを修飾するモジュールの要素への参照が明示的に必要なことを示します。 たとえば、`Microsoft.FSharp.Collections.List`モジュールにはこの属性があります。

これは、機能は、関数と、モジュール内の値は、他のモジュール名と競合する可能性のある名前を持つ場合に便利です。 修飾のアクセスを必要とすると、長期的な保守容易性とライブラリの発展が大幅に向上できます。

追加、`[<AutoOpen>]`モジュールに属性が含まれる名前空間が開かれたときに、モジュールが開かれることを意味します。 `[<AutoOpen>]`属性は、アセンブリ、アセンブリが参照されている場合に自動的に開かれているモジュールを示すためにも適用できます。

統計ライブラリではたとえば、 **MathsHeaven.Statistics**含めることができます、`module MathsHeaven.Statistics.Operators`関数を含む`erf`と`erfc`します。 このモジュールとしてマークするが妥当`[<AutoOpen>]`します。 つまり、`open MathsHeaven.Statistics`もがこのモジュールを開き、名前を`erf`と`erfc`スコープにします。 別なを使用して、`[<AutoOpen>]`拡張メソッドが含まれるモジュールです。

多用する`[<AutoOpen>]`汚染の名前空間と属性に潜在顧客を注意して使用する必要があります。 特定のドメインを賢く利用で特定のライブラリの`[<AutoOpen>]`ユーザビリティは向上する可能性があります。

#### <a name="consider-defining-operator-members-on-classes-where-using-well-known-operators-is-appropriate"></a>よく知られている演算子を使用して、適切なクラスで演算子メンバーの定義を検討してください。

場合によってベクトルなどの数学的構造をモデル化するクラスが使用されます。 モデル化されるドメインによく知られている演算子がある場合、クラスに固有のメンバーとして定義することお勧めします。

```fsharp
type Vector(x: float) =

    member v.X = x

    static member (*) (vector: Vector, scalar: float) = Vector(vector.X * scalar)

    static member (+) (vector1: Vector, vector2: Vector) = Vector(vector1.X + vector2.X)

let v = Vector(5.0)

let u = v * 10.0
```

このガイダンスは、このような種類の一般的な .NET ガイダンスに対応します。 ただし、F# コードこれにより、これらの型の F# 関数と組み合わせておよび List.sumBy などのメンバー制約でのメソッドで使用するようにさらに重要ですができます。

#### <a name="consider-using-compiledname-to-provide-a-net-friendly-name-for-other-net-language-consumers"></a>提供する CompiledName の使用を検討します。その他の .NET 言語のコンシューマーの NET のわかりやすい名前

名前が必要な 1 つのスタイルをする場合がありますF#コンシューマー (などを小文字で静的メンバー モジュール連結関数の場合と同様) がアセンブリにコンパイルすると、名前の別のスタイルが。 使用することができます、`[<CompiledName>]`非 F# アセンブリを使用するコードのさまざまなスタイルを指定する属性。

```fsharp
type Vector(x:float, y:float) =

    member v.X = x
    member v.Y = y

    [<CompiledName("Create")>]
    static member create x y = Vector (x, y)

let v = Vector.create 5.0 3.0
```

使用して`[<CompiledName>]`、非 F# アセンブリのコンシューマー向けの .NET の名前付け規則を使用することができます。

#### <a name="use-method-overloading-for-member-functions-if-doing-so-provides-a-simpler-api"></a>メンバー関数の場合は、メソッドのオーバー ロードを使用するため、これによりより単純な API の場合

メソッドのオーバー ロードは、さまざまなオプションや引数が、同様の機能を実行する必要がある API を簡略化するための強力なツールです。

```fsharp
type Logger() =

    member this.Log(message) =
        ...
    member this.Log(message, retryPolicy) =
        ...
```

F#、引数の型ではなく、引数の数にオーバー ロードする方が一般的です。

#### <a name="hide-the-representations-of-record-and-union-types-if-the-design-of-these-types-is-likely-to-evolve"></a>これらの型のデザインが発展する可能性がある場合、レコード、共用体の型の表現を非表示にします。

オブジェクトの具体的な表現に表示されないようにします。 具体的な表現など<xref:System.DateTime>値は .NET ライブラリの設計の外部、パブリック API によって公開されません。 実行時に、共通言語ランタイムは、実行全体で使用されるコミットの実装を認識します。 ただし、コンパイル済みのコードはしない自体具体的な表現への依存関係を選択します。

#### <a name="avoid-the-use-of-implementation-inheritance-for-extensibility"></a>機能拡張の実装の継承は使用しないでください。

F#、実装の継承はほとんど使用しません。 さらに、継承階層は多くの場合、複雑で、新しい要件が到着したときに変更が困難にします。 継承の実装は、互換性を維持し、まれなケースで、問題に最適なソリューションですが、インターフェイスの実装などのポリモーフィズムの設計時に、F# プログラムで代替手法を検索する必要があります F# でも存在します。

### <a name="function-and-member-signatures"></a>関数とメンバーのシグネチャ

#### <a name="use-tuples-for-return-values-when-returning-a-small-number-of-multiple-unrelated-values"></a>複数の関連のない値の数が少ないを返すときに、戻り値のタプルを使用します。

戻り値の型で組を使用する良い例を次に示します。

```fsharp
val divrem: BigInteger -> BigInteger -> BigInteger * BigInteger
```

多くのコンポーネントを含む型を返すかを特定できる単一のエンティティには、コンポーネントが関係する場合は、タプルではなく名前付きの型を使用することを検討してください。

#### <a name="use-asynct-for-async-programming-at-f-api-boundaries"></a>使用`Async<T>`F# API の境界での非同期プログラミング

という名前の対応する同期操作があるかどうかは`Operation`を返す、 `T`、非同期操作を指定する必要がありますし、`AsyncOperation`返された場合`Async<T>`または`OperationAsync`返された場合`Task<T>`します。 Begin/end のメソッドを公開に一般的な .NET 型を使用を検討してください`Async.FromBeginEnd`.NET Api を F# 非同期プログラミング モデルを提供するファサードとして拡張メソッドを記述します。

```fsharp
type SomeType =
    member this.Compute(x:int): int =
        ...
    member this.AsyncCompute(x:int): Async<int> =
        ...

type System.ServiceModel.Channels.IInputChannel with
    member this.AsyncReceive() =
        ...
```

### <a name="exceptions"></a>例外

参照してください[エラー管理](conventions.md#error-management)に、例外、結果、およびオプションの適切な使用について説明します。

### <a name="extension-members"></a>拡張メンバー

#### <a name="carefully-apply-f-extension-members-in-f-to-f-components"></a>F# での F# の拡張メンバーを注意深く適用-に-F# コンポーネント

F#拡張メンバーは、通常のみ使用のほとんどの使用のモードの種類に関連付けられた組み込みの操作のクロージャに含まれる操作。 一般的な用途の 1 つはさまざまな .NET 型の F# 慣用 Api を提供することです。

```fsharp
type System.ServiceModel.Channels.IInputChannel with
    member this.AsyncReceive() =
        Async.FromBeginEnd(this.BeginReceive, this.EndReceive)

type System.Collections.Generic.IDictionary<'Key,'Value> with
    member this.TryGet key =
        let ok, v = this.TryGetValue key
        if ok then Some v else None
```

### <a name="union-types"></a>共用体の型

#### <a name="use-discriminated-unions-instead-of-class-hierarchies-for-tree-structured-data"></a>判別共用体をクラスの階層構造ではなくツリー構造のデータの使用します。

ツリー状の構造体は、定義されている再帰的にします。 これは、継承、厄介なが判別共用体で洗練されました。

```fsharp
type BST<'T> =
    | Empty
    | Node of 'T * BST<'T> * BST<'T>
```

判別共用体のツリーのようなデータを表すではパターン マッチングで exhaustiveness からメリットを得ることもできます。

#### <a name="use-requirequalifiedaccess-on-union-types-whose-case-names-are-not-sufficiently-unique"></a>使用`[<RequireQualifiedAccess>]`で名前を持つケースが十分に一意でない共用体の型

同じ名前が判別された共用体ケースなどのさまざまなもののわかりやすい名前をドメインでわかった可能性があります。 使用することができます`[<RequireQualifiedAccess>]`トリガーの順序に依存にシャドウによる混乱を招くエラーを回避するために名前の大文字と小文字を区別する`open`ステートメント

#### <a name="hide-the-representations-of-discriminated-unions-for-binary-compatible-apis-if-the-design-of-these-types-is-likely-to-evolve"></a>これらの型のデザインが発展する可能性がある場合、バイナリの互換性のある Api の判別共用体の表現を非表示にします。

共用体の型は、F# のパターンに一致するフォームの簡潔なプログラミング モデルに依存します。 前述のように、これらの型のデザインが発展する可能性がある場合は、具体的なデータ表現を漏洩を避ける必要があります。

プライベートまたは内部の宣言を使用して判別共用体の表現を隠すことが、または署名ファイルを使用しています。

```fsharp
type Union =
    private
    | CaseA of int
    | CaseB of string
```

判別共用体を無差別に開示する場合した方のバージョンにハード ライブラリ ユーザー コードを損なうことがなく。 代わりに、型の値に一致するパターンを許可するように 1 つまたは複数のアクティブ パターンを明らかに検討してください。

アクティブ パターンでは、F# 共用体の型を直接公開することを回避しながらに一致するパターンを F# でコンシューマーに提供する別の方法を提供します。

### <a name="inline-functions-and-member-constraints"></a>インライン関数とメンバー制約

#### <a name="define-generic-numeric-algorithms-using-inline-functions-with-implied-member-constraints-and-statically-resolved-generic-types"></a>暗黙的なメンバー制約とジェネリック型を静的に解決されるインライン関数を使用して、汎用的な数値アルゴリズムを定義します。

算術メンバー制約と F# 比較の制約は、F# プログラミングの標準です。 次に例を示します。

```fsharp
let inline highestCommonFactor a b =
    let rec loop a b =
        if a = LanguagePrimitives.GenericZero<_> then b
        elif a < b then loop a (b - a)
        else loop (a - b) b
    loop a b
```

この関数の型は次のとおりです。

```fsharp
val inline highestCommonFactor : ^T -> ^T -> ^T
                when ^T : (static member Zero : ^T)
                and ^T : (static member ( - ) : ^T * ^T -> ^T)
                and ^T : equality
                and ^T : comparison
```

これは、数値演算ライブラリ内のパブリック API の適切な関数です。

#### <a name="avoid-using-member-constraints-to-simulate-type-classes-and-duck-typing"></a>型のクラスとアヒル型定義をシミュレートするためにメンバー制約は使用しないでください。

「ダック タイピング」をシミュレートすることは F# メンバー制約を使用します。 ただし、メンバーを使用してこのにない全般を F# で使用する必要があります-に-F# ライブラリ設計します。 これは、未知または標準の暗黙的な制約に基づいてライブラリ設計を強いと特定のフレームワークを 1 つのパターンに関連付けられていますが、ユーザー コードが発生する傾向があるためにです。

さらに、非常に長いコンパイル時間の結果としてこの方法でメンバーの制約を頻繁に使用される可能性があります。

### <a name="operator-definitions"></a>演算子の定義

#### <a name="avoid-defining-custom-symbolic-operators"></a>シンボリック カスタムの演算子を定義しないでください。

カスタム演算子はいくつかの状況で不可欠な実装コードの大きな本文内で非常に有用な表記のデバイスです。 ライブラリの新しいユーザーの名前付きの関数は使いやすく多くの場合です。 さらに、ユーザーを検索演算子、IDE と検索エンジンの既存の制限のためのヘルプを検索することが難しく、シンボリック カスタムの演算子をドキュメントにハードことができます。

その結果、名前付きの関数と、メンバーとして、機能を公開し、表記の利点がありますが、ドキュメントと cognitive コストのことを上回る場合にのみ、この機能のための演算子をさらに公開することをお勧めします。

### <a name="units-of-measure"></a>測定単位

#### <a name="carefully-use-units-of-measure-for-added-type-safety-in-f-code"></a>追加された型の安全性の測定単位を使用して、慎重にF#コード

他の .NET 言語で表示したときに、メジャーの単位の追加入力情報が消去されます。 .NET コンポーネント、ツール、およびリフレクションは型-san の単位で表示されますが注意します。 たとえば、c# のコンシューマーが表示されます`float`なく`float<kg>`します。

### <a name="type-abbreviations"></a>型略称

#### <a name="carefully-use-type-abbreviations-to-simplify-f-code"></a>型の省略形を使用して慎重に F# コードを簡略化

.NET コンポーネント、ツール、およびリフレクションでは、型の省略名は表示されなくなります。 型の省略形の使用量を大幅には、ドメインの表示よりも複雑になるほど実際には、コンシューマーが混乱することもできます。

#### <a name="avoid-type-abbreviations-for-public-types-whose-members-and-properties-should-be-intrinsically-different-to-those-available-on-the-type-being-abbreviated"></a>メンバーとプロパティが省略されている型で使用するものは本質的に異なる必要がありますのパブリック型の型の省略形を回避します。

この場合、省略されている型は定義されている実際の型の表現についてはあまりが表示されます。 代わりに、クラス型または単一ケースの判別共用体の省略形の折り返しを検討してください (または、パフォーマンスが重要な場合は、構造体の型を使用して省略形をラップすることを検討してください)。

たとえば、複数のマップの例の F# マップの特殊なケースとして定義することも考えは。

```fsharp
type MultiMap<'Key,'Value> = Map<'Key,'Value list>
```

ただし、この種類でドット表記の論理操作でないマップでの操作と同じ – たとえば、lookup 操作にマップする妥当なは。[キー] に返された例外を発生させるのではなく、辞書にキーがない場合は、空のリスト。

## <a name="guidelines-for-libraries-for-use-from-other-net-languages"></a>他の .NET 言語から使用するためのライブラリのガイドライン

他の .NET 言語から使用するためのライブラリを設計するときに準拠する重要なは、 [.NET ライブラリのデザイン ガイドライン](../../standard/design-guidelines/index.md)します。 このドキュメントでは、これらのライブラリを F# ではなく、標準の .NET ライブラリとしてラベル付けします。-F# を使用してライブラリに接続する、制限なく構築します。 Vanilla .NET ライブラリのデザインには、F# の使用を最小限に抑えることで親しみやすく、慣用 Api の .NET Framework の残りの部分と一貫性を提供することを意味のパブリック API で特定のコンス トラクター。 ルールは、次のセクションについて説明します。

### <a name="namespace-and-type-design-for-libraries-for-use-from-other-net-languages"></a>(他の .NET 言語から使用するためのライブラリ) の Namespace および型の設計

#### <a name="apply-the-net-naming-conventions-to-the-public-api-of-your-components"></a>コンポーネントのパブリック API を .NET の名前付け規則を適用します。

省略名と、.NET の大文字と小文字のガイドラインを使用する特別な注意してください。

```fsharp
type pCoord = ...
    member this.theta = ...

type PolarCoordinate = ...
    member this.Theta = ...
```

#### <a name="use-namespaces-types-and-members-as-the-primary-organizational-structure-for-your-components"></a>主要な組織構造として、コンポーネントの名前空間、型、およびメンバーを使用します。

パブリックな機能を含むすべてのファイルで始まり、`namespace`宣言、および名前空間でのみパブリックに公開されたエンティティに、型を指定する必要があります。 使用しないF#モジュール。

実装コード、ユーティリティの種類、およびユーティリティ関数を保持するのにには、パブリックでないモジュールを使用します。

静的な型に優先されますモジュールを可能になる API の今後の進化 F# モジュール内で使用することはできませんをオーバー ロード、およびその他の .NET API 設計概念を使用するとします。

たとえば、次のパブリック API: の代わりに

```fsharp
module Fabrikam

module Utilities =
    let Name = "Bob"
    let Add2 x y = x + y
    let Add3 x y z = x + y + z
```

代わりに検討してください。

```fsharp
namespace Fabrikam

[<AbstractClass; Sealed>]
type Utilities =
    static member Name = "Bob"
    static member Add(x,y) = x + y
    static member Add(x,y,z) = x + y + z
```

#### <a name="use-f-record-types-in-vanilla-net-apis-if-the-design-of-the-types-wont-evolve"></a>型のデザインが進化しない場合は、バニラ .NET Api で F# レコード型を使用してください。

F#レコードの種類は、単純な .NET クラスをコンパイルします。 これらは Api のいくつか単純で安定した型に適しています。 使用を検討する必要があります、`[<NoEquality>]`と`[<NoComparison>]`インターフェイスの自動生成を抑制する属性。 また、パブリック フィールドとしてこれらの公開バニラ .NET Api の変更可能なレコードのフィールドを使用しないでください。 常にクラスが API の将来の進化をより柔軟なオプションを提供するかどうかを検討してください。

たとえば、次の F# コードは、C# コンシューマーへのパブリック API を公開します。

F#: 

```fsharp
[<NoEquality; NoComparison>]
type MyRecord =
    { FirstThing: int
        SecondThing: string }
```

C#: 

```csharp
public sealed class MyRecord
{
    public MyRecord(int firstThing, string secondThing);
    public int FirstThing { get; }
    public string SecondThing { get; }
}
```

#### <a name="hide-the-representation-of-f-union-types-in-vanilla-net-apis"></a>表現を非表示にF#バニラ .NET Api で共用体の型

F# の共用体型されません一般的に使用されるコンポーネントの境界を越えても F#-に-F# コーディングします。 コンポーネントとライブラリ内で内部的に使用する場合は、優れた実装デバイスです。

Vanilla .NET API を設計するときは、プライベート宣言または署名ファイルを使用して、union 型の表現を非表示を検討してください。

```fsharp
type PropLogic =
    private
    | And of PropLogic * PropLogic
    | Not of PropLogic
    | True
```

提供を目的に、メンバーと共用体の表現を内部的に使用する型を追加することも可能性があります。ネットワークに接続する API です。

```fsharp
type PropLogic =
    private
    | And of PropLogic * PropLogic
    | Not of PropLogic
    | True

    /// A public member for use from C#
    member x.Evaluate =
        match x with
        | And(a,b) -> a.Evaluate && b.Evaluate
        | Not a -> not a.Evaluate
        | True -> true

    /// A public member for use from C#
    static member CreateAnd(a,b) = And(a,b)
```

#### <a name="design-gui-and-other-components-using-the-design-patterns-of-the-framework"></a>GUI を設計およびフレームワークの設計パターンを使用して他のコンポーネント

多くのさまざまなフレームワークは WinForms、WPF、および ASP.NET などの .NET 内で使用できます。 これらのフレームワークで使用するためのコンポーネントをデザインする場合は、それぞれの名前付けとデザインの規則を使用してください。 たとえば、wpf プログラミングでは、クラスを設計するための WPF 設計パターンを採用します。 ユーザー インターフェイス プログラミング モデルの場合、イベントなどの設計パターンを使用しなどの通知ベースのコレクションにある<xref:System.Collections.ObjectModel>します。

### <a name="object-and-member-design-for-libraries-for-use-from-other-net-languages"></a>(他の .NET 言語から使用するためのライブラリ) のオブジェクトとメンバーのデザイン

#### <a name="use-the-clievent-attribute-to-expose-net-events"></a>CLIEvent 属性を使用して、.NET イベントを公開するには

構築、`DelegateEvent`特定の .NET を使用したデリゲート オブジェクトを取得する型と`EventArgs`(なく`Event`、使用する、`FSharpHandler`既定で種類) イベントが他の .NET 言語を使い慣れた方法で公開されるため。

```fsharp
type MyBadType() =
    let myEv = new Event<int>()

    [<CLIEvent>]
    member this.MyEvent = myEv.Publish

type MyEventArgs(x: int) =
    inherit System.EventArgs()
    member this.X = x

    /// A type in a component designed for use from other .NET languages
type MyGoodType() =
    let myEv = new DelegateEvent<EventHandler<MyEventArgs>>()

    [<CLIEvent>]
    member this.MyEvent = myEv.Publish
```

#### <a name="expose-asynchronous-operations-as-methods-which-return-net-tasks"></a>.NET のタスクを返すメソッドとしての非同期操作を公開します。

タスクは、アクティブな非同期計算を表す .NET で使用されます。 タスクは、一般的な F# よりも小さい合成`Async<T>`オブジェクトの場合、「は既に実行中」のタスクを表すされ、並列の合成を実行するか、キャンセルの信号、およびその他の伝達を非表示にする方法で一緒に構成することはできませんコンテキスト パラメーター。

ただし、この場合でもタスクを返すメソッドは .NET での非同期プログラミングの標準的な表現です。

```fsharp
/// A type in a component designed for use from other .NET languages
type MyType() =

    let compute (x: int): Async<int> = async { ... }

    member this.ComputeAsync(x) = compute x |> Async.StartAsTask
```

多くの場合は、明示的なキャンセル トークンをそのまま使用することもします。

```fsharp
/// A type in a component designed for use from other .NET languages
type MyType() =
    let compute(x: int): Async<int> = async { ... }
    member this.ComputeAsTask(x, cancellationToken) = Async.StartAsTask(compute x, cancellationToken)
```

#### <a name="use-net-delegate-types-instead-of-f-function-types"></a>代わりに .NET のデリゲート型を使用して、F#関数の型

ここで「F# 関数型」という意味で「矢印」種類`int -> int`します。

これには。

```fsharp
member this.Transform(f: int->int) =
    ...
```

方法 :

```fsharp
member this.Transform(f: Func<int,int>) =
    ...
```

として F# 関数型が表示されます`class FSharpFunc<T,U>`他の .NET 言語にはデリゲート型を理解できる言語機能とツールには適しています。 .NET Framework 3.5 以降を対象とする上位のメソッドを作成するときに、`System.Func`と`System.Action`デリゲートは、問題の少ない方法でこれらの Api を使用する .NET 開発者に公開する適切な Api。 (システム定義のデリゲート型がより限定的な .NET Framework 2.0 をターゲットにした場合など、定義済みのデリゲート型の使用を検討する必要が`System.Converter<T,U>`または特定のデリゲート型を定義します)。

一方で、.NET のデリゲートではない F# のライブラリに接続する (F# で、次のセクションを参照してください-ライブラリに接続する)。 すべて F# 関数型を使用して実装を作成し、一番上にある実際の F# シン ファサードとしてデリゲートを使用してパブリック API を作成する標準の .NET ライブラリの上位のメソッドを開発する際に一般的な実装方法は、結果として、します。実装です。

#### <a name="use-the-trygetvalue-pattern-instead-of-returning-f-option-values-and-prefer-method-overloading-to-taking-f-option-values-as-arguments"></a>F# オプションの値を返す代わりに、TryGetValue パターンを使用し、引数としての F# のオプション値を取得するメソッドのオーバー ロードを優先

Api で F# オプションの種類の使用の一般的なパターンはより優れたバニラで実装された標準の .NET を使用した .NET Api の設計手法です。 返す代わりに、F#オプションの値、ブール型の戻り値の型と"TryGetValue"パターンのように out パラメーターの使用を検討します。 作成ではなくF#オプション パラメーターとして値をメソッドのオーバー ロードまたは省略可能な引数を使用して検討してください。

```fsharp
member this.ReturnOption() = Some 3

member this.ReturnBoolAndOut(outVal: byref<int>) =
    outVal <- 3
    true

member this.ParamOption(x: int, y: int option) =
    match y with
    | Some y2 -> x + y2
    | None -> x

member this.ParamOverload(x: int) = x

member this.ParamOverload(x: int, y: int) = x + y
```

#### <a name="use-the-net-collection-interface-types-ienumerablet-and-idictionarykeyvalue-for-parameters-and-return-values"></a>使用して、.NET コレクション インターフェイス型の IEnumerable\<T\>と IDictionary\<キー, Value\>パラメーターと戻り値

.NET の配列などの具体的なコレクション型の使用を避けるため`T[]`、F# 型`list<T>`、`Map<Key,Value>`と`Set<T>`などの .NET の具体的なコレクション型と`Dictionary<Key,Value>`します。 .NET ライブラリのデザイン ガイドラインがあるなどのさまざまなコレクション型を使用するタイミングに関する優れたアドバイス`IEnumerable<T>`します。 配列のいくつかの使用 (`T[]`) は状況によっては、パフォーマンスの理由で許容されます。 特に注意`seq<T>`同様、F# のエイリアスは`IEnumerable<T>`、したがって seq は多くの場合、バニラ .NET API の適切な型。

代わりに F# の一覧です。

```fsharp
member this.PrintNames(names: string list) =
    ...
```

使用F#シーケンス。

```fsharp
member this.PrintNames(names: seq<string>) =
    ...
```

#### <a name="use-the-unit-type-as-the-only-input-type-of-a-method-to-define-a-zero-argument-method-or-as-the-only-return-type-to-define-a-void-returning-method"></a>メソッドの唯一の入力型としてユニットの種類を使用して、引数を持たないメソッドを定義またはのみとして void を返すメソッドを定義する型を返す

ユニットの種類の他の使用を避けてください。 これらは、適切です。

```fsharp
✔ member this.NoArguments() = 3

✔ member this.ReturnVoid(x: int) = ()
```

これには。

```fsharp
member this.WrongUnit( x: unit, z: int) = ((), ())
```

#### <a name="check-for-null-values-on-vanilla-net-api-boundaries"></a>標準の .NET API の境界上で null 値のチェック

F# 実装コードに不変の設計パターンと F# の型の null リテラルの使用に関する制約のため、null の値が少ない傾向にあります。 他の .NET 言語多くの場合、値として null より頻繁に使用します。 このため、バニラ .NET API を公開する F# コードを API の境界の null のパラメーターを確認し、これらの値が F# 実装コードに深くを送信するを防ぐ。 `isNull`関数またはパターンに一致する、`null`パターンを使用できます。

```fsharp
let checkNonNull argName (arg: obj) =
    match arg with
    | null -> nullArg argName
    | _ -> ()

let checkNonNull` argName (arg: obj) =
    if isNull arg then nullArg argName
    else ()
```

#### <a name="avoid-using-tuples-as-return-values"></a>戻り値として組を使用しないでください。

代わりに、集計のデータを保持するか、複数の値を返す出力パラメーターを使用して名前付きの型を返すことを好みます。 タプルと構造体のタプルは、.NET に存在 (c# の構造体のタプルの言語サポートを含む)、最もよくが用意されて、理想的なと予想される API .NET 開発者向け。

#### <a name="avoid-the-use-of-currying-of-parameters"></a>パラメーターのカリー化を使用をしません。

代わりに、.NET の呼び出し規約を使用して、`Method(arg1,arg2,…,argN)`します。

```fsharp
member this.TupledArguments(str, num) = String.replicate num str
```

ヒント :任意の .NET 言語から使用するためのライブラリを設計するかどうかは、実験の一部が実際に行ってものはありませんC#および Visual Basic のプログラミング ライブラリ「フィール右」これらの言語からことを確認します。 ライブラリとそのドキュメントが開発者に期待どおりに表示されていることを確認するのに、.NET Reflector Visual Studio オブジェクト ブラウザーなどのツールを使用することもできます。

## <a name="appendix"></a>付録

### <a name="end-to-end-example-of-designing-f-code-for-use-by-other-net-languages"></a>他の .NET 言語で使用するために F# コードの設計のエンド ツー エンドの例

次のクラスを検討してください。

```fsharp
open System

type Point1(angle,radius) =
    new() = Point1(angle=0.0, radius=0.0)
    member x.Angle = angle
    member x.Radius = radius
    member x.Stretch(l) = Point1(angle=x.Angle, radius=x.Radius * l)
    member x.Warp(f) = Point1(angle=f(x.Angle), radius=x.Radius)
    static member Circle(n) =
        [ for i in 1..n -> Point1(angle=2.0*Math.PI/float(n), radius=1.0) ]
```

このクラスの推定の F# 型は次のとおりです。

```fsharp
type Point1 =
    new : unit -> Point1
    new : angle:double * radius:double -> Point1
    static member Circle : n:int -> Point1 list
    member Stretch : l:double -> Point1
    member Warp : f:(double -> double) -> Point1
    member Angle : double
    member Radius : double
```

この F# 型が別の .NET 言語を使用してプログラマに表示する方法を見てをみましょう。 たとえば、おおよそ C#「署名」とおりです。

```csharp
// C# signature for the unadjusted Point1 class
public class Point1
{
    public Point1();

    public Point1(double angle, double radius);

    public static Microsoft.FSharp.Collections.List<Point1> Circle(int count);

    public Point1 Stretch(double factor);

    public Point1 Warp(Microsoft.FSharp.Core.FastFunc<double,double> transform);

    public double Angle { get; }

    public double Radius { get; }
}
```

方法について注意する重要な点があるF#表しますをここで構築します。 例:

* 引数名などのメタデータが保持されています。

* 2 つの引数を取る F# メソッドでは、2 つの引数を取る C# メソッドになります。

* 関数とリストは、対応する型が F# ライブラリへの参照になります。

次のコードでは、このコードは、これらの点を考慮に入れてを調整する方法を示します。

```fsharp
namespace SuperDuperFSharpLibrary.Types

type RadialPoint(angle:double, radius:double) =

    /// Return a point at the origin
    new() = RadialPoint(angle=0.0, radius=0.0)

    /// The angle to the point, from the x-axis
    member x.Angle = angle

    /// The distance to the point, from the origin
    member x.Radius = radius

    /// Return a new point, with radius multiplied by the given factor
    member x.Stretch(factor) =
        RadialPoint(angle=angle, radius=radius * factor)

    /// Return a new point, with angle transformed by the function
    member x.Warp(transform:Func<_,_>) =
        RadialPoint(angle=transform.Invoke angle, radius=radius)

    /// Return a sequence of points describing an approximate circle using
    /// the given count of points
    static member Circle(count) =
        seq { for i in 1..count ->
                RadialPoint(angle=2.0*Math.PI/float(count), radius=1.0) }
```

コードの推定の F# 型は次のとおりです。

```fsharp
type RadialPoint =
    new : unit -> RadialPoint
    new : angle:double * radius:double -> RadialPoint
    static member Circle : count:int -> seq<RadialPoint>
    member Stretch : factor:double -> RadialPoint
    member Warp : transform:System.Func<double,double> -> RadialPoint
    member Angle : double
    member Radius : double
```

C# シグネチャは次のように、ようになりました。

```csharp
public class RadialPoint
{
    public RadialPoint();

    public RadialPoint(double angle, double radius);

    public static System.Collections.Generic.IEnumerable<RadialPoint> Circle(int count);

    public RadialPoint Stretch(double factor);

    public RadialPoint Warp(System.Func<double,double> transform);

    public double Angle { get; }

    public double Radius { get; }
}
```

Vanilla .NET ライブラリの一部は次のように、この型を使用する準備を修正します。

* いくつかの名前を調整: `Point1`、 `n`、 `l`、および`f`になった`RadialPoint`、 `count`、 `factor`、および`transform`、それぞれします。

* 戻り値の型を使用する`seq<RadialPoint>`の代わりに`RadialPoint list`を使用してリストの構築を変更することで`[ ... ]`を使用してシーケンスの構築に`IEnumerable<RadialPoint>`します。

* .NET のデリゲート型を使用する`System.Func`F# 関数型の代わりにします。

これにより、c# コードで使用するよりはるかにします。
