---
title: ポリモーフィズム/多態性/Polymorphism
layout: default
tags:  [csharp,Polymorphism,oop]
---


## C#におけるポリモーフィズムとは？

### ポリモーフィズムとは

ポリモーフィズムとは、**多態性**と訳され、オブジェクト指向プログラミングにおける重要な概念の一つです。同じ名前のメソッドが、異なるクラスで異なる動作をすることを意味します。

もう少し具体的に言うと、基底クラスで定義されたメソッドを、派生クラスでオーバーライドすることで、それぞれのクラスに合った処理を実行することができます。これにより、プログラムの柔軟性と拡張性が大幅に向上します。

### ポリモーフィズムの種類

C#では、主に以下の2種類のポリモーフィズムが利用できます。

#### 1. メソッドのオーバーライド
* 基底クラスで定義されたメソッドを、派生クラスで同じシグネチャ（Signature）（メソッド名、引数の型、戻り値の型）で再定義することです。
* 派生クラスのオブジェクトに対してメソッドを呼び出すと、オーバーライドされたメソッドが実行されます。
>メソッドのシグネチャ:
プログラミング言語において、関数やメソッドの名前、引数の型、戻り値の型などをまとめたもの。
プログラムの構造を定義し、異なる関数やメソッドを区別する際に使用されます。

```csharp
class Animal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("動物の鳴き声");
    }
}

class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("ワンワン");
    }
}

class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("ニャーニャー");
    }
}
```

#### 2. メソッドのオーバーロード(Overloads)
* 同じクラス内で、同じメソッド名で、引数の型や数が異なる複数のメソッドを定義することです。
* 呼び出し時に渡される引数の型や数によって、実行されるメソッドが決定されます。

```csharp
class Calculator
{
    public int Add(int x, int y)
    {
        return x + y;
    }

    public double Add(double x, double y)
    {
        return x + y;
    }
}
```

### ポリモーフィズムのメリット

* **コードの再利用性向上:** 共通のインターフェースを持つことで、異なるオブジェクトを同じように扱うことができます。
* **拡張性の向上:** 新しいクラスを追加する際に、既存のコードを大幅に修正する必要がありません。
* **可読性の向上:** コードの意図がより明確になります。

### ポリモーフィズムの活用例

* **図形を描くプログラム:** 異なる図形（円、四角形など）を、共通の基底クラス（図形）から派生させ、それぞれの図形を描画するメソッドをオーバーライドします。
* **データベースアクセス:** 異なるデータベース（SQL Server, MySQLなど）にアクセスするクラスを、共通のインターフェース（データベースアクセス）から派生させ、共通の操作を提供します。

### まとめ

ポリモーフィズムは、C#プログラミングにおいて非常に強力なツールです。この概念を理解し、適切に活用することで、より柔軟で拡張性の高いプログラムを作成することができます。

**ポイント:**
* ポリモーフィズムは、オブジェクト指向プログラミングの三大特徴の一つです。
* メソッドのオーバーライドとオーバーロードは、それぞれ異なる概念ですが、どちらもポリモーフィズムを実現するための手段です。
* ポリモーフィズムを効果的に活用することで、コードの品質向上に繋がります。

**さらに詳しく知りたい場合は、以下のキーワードで検索してみてください。**

* C# ポリモーフィズム
* オーバーライド
* オーバーロード
* オブジェクト指向プログラミング

何か他に質問はありますか？
