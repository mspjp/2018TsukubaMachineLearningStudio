![](https://github.com/MSFTImagine/computerscience/raw/master/Event-In-Box/Machine%20Learning%20Challenge/Images/ml-challenge.png)

# 概要
Machine Learning Challenge では、[Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio/)を使用して、主要な米国航空会社の到着時刻データを利用したシンプルな（そして非常に正確な）機械学習のモデルを構築します。
そのときには、自分自身でモデルをチューニングして精度を高めます。
目標は、所定のフライトが到着予定時刻通りに到着する可能性を予測するための、実世界で有用なモデルを作成することです。

## 前提条件
- Microsoft のアカウント（[https://account.microsoft.com/account]()）
- Microsoft Edge や Google Chrome などの最新のブラウザ


# 演習
Machine Learning Challenge では、以下の演習を実施します。
- 演習1：機械学習の実験を作成する
- 演習2：データセットを読み込む
- 演習3：分類モデルを訓練する
- 演習4：モデルをチューニングする


# 演習1：機械学習の実験を作成する
最初のステップでは、Azure Machine Learning Studio を使用するために、機械学習の実験（Experiment）を以下の手順で作成します。

1. Webブラウザで[http://studio.azureml.net/]()に移動し、[Sign Up]をクリックします。
![](./Images/sign-up.jpg)

1. Microsoft アカウントを使用してサインインします。
![](./Images/sign-in.jpg)

1. ページの左下にある[+ NEW]をクリックし、[Blank Experiment]をクリックして、新しい実験を作成します。
![](./Images/ml-studio.jpg)
![](./Images/new.jpg)

1. ページ上部の実験タイトル（Experiment created on ...）をクリックし、新しい実験名として「ML Challenge」と入力します。
![](./Images/experiment.jpg)

これで、機械学習のモデルを構築、訓練およびテストすることができる機械学習の実験を作成できました。
次のステップでは、訓練とテストに使用するためのデータセットをアップロードします。




# 演習4：モデルをチューニングする
ここからが楽しいところです。
データサイエンティストの帽子をかぶって、高い精度がでるようにモデルを調整しましょう。
目標は<b>AUCを0.75以上</b>にすることです。
しかし、それには専門知識が必要です。

演習4では、その目標を達成するために6つのヒントを提供します。
これらのヒントは、熟練のデータサイエンティストがより堅牢なモデルを作成するために用いるテクニックです。
これらはAUCを0.75にするための唯一の方法ではありませんが、モデルの調整を始めるのに適しています。


## ヒント1：異なるアルゴリズムを試す
ML Studio にはモデルに使用できる9つの2値分類アルゴリズムが含まれています。
どのアルゴリズムが最良の結果をもたらすかは、熟練したデータサイエンティストでさえも試してみるまで分からないことがあります。
Azure Machine Learning では、異なるアルゴリズムを試すことは簡単で、あるモジュールを別のモジュールと交換するだけです。
これは Azure Machine Learning の強みの1つです。


演習3で作成したモデルは [Two-Class Logistic Regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) を使用しています。
Two-Class Logistic Regression は回帰を使用して2つの最良の結果の確率を計算する一般的なアルゴリズムです。
最適な結果を得るために異なるアルゴリズムを試してみてください。
Microsoft は予測モデルに適切なアルゴリズムを選ぶときに役立つアルゴリズムチートシートを提供しています。
[ここをクリック](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)してダウンロードしてください。

![cheat-sheet](https://github.com/Microsoft/computerscience/raw/master/Events/Event-In-Box/Machine%20Learning%20Challenge/Images/cheat-sheet.png)



## ヒント2：キャンセルされたフライトと迂回されたフライトの影響を緩和する

使用しているデータセットには、キャンセルされたフライトまたは迂回されたフライトを表すデータが約200件含まれています。
これらのフライトは、演習3の [Select Columns in Dataset](https://msdn.microsoft.com/library/azure/dn905883.aspx) モジュールで除外した CANCELLED または DIVERTED の列の値が1で表されています。
キャンセルされたフライトまたは迂回されたフライトを表す行には ARR_DEL15 の値がありません。
このことはデータセットとその結果を歪ませます。
可視化した ARR_DEL15 の値を下図に示します。
ARR_DEL15 の値は2値（0か1）のつもりですが、実際には188個の欠損値を含めた3つのユニークな値があることに注意してください。

![画像：The ARR_DEL15 column]()

これを解決する方法はいくつかあります。
ひとつは、欠損した ARR_DEL15 の値を持つ行を削除したり、時間どおりに到着しなかったフライトの ARR_DEL15 の値を1に置き換えたりする R や Python のスクリプトを書くことです。
そして、[Execute R Script](https://msdn.microsoft.com/library/azure/dn905952.aspx) モジュールか [Execute Python Script](https://msdn.microsoft.com/library/azure/dn955437.aspx) モジュールを使用して、そのスクリプトをモデルに組込みます。

あるいは、キャンセルされたフライトまたは迂回されたフライトを表す各行には欠損値（列にデータがない）があるため、[Clean Missing Data](https://msdn.microsoft.com/library/azure/dn906028.aspx) モジュールを使用することができます。
このモジュールを使用すると、欠損値を置き換えたり、欠損値を含む行を完全に削除したりすることができます。


## ヒント3: データセットのバランスの悪さを減らす
2クラス分類モデルを訓練するために使用されるデータは Positive と Negative を半分づつ含んでいることが理想です。
しかし、現実の世界でそうなっていることはまれです。
バランスの悪いデータセットは（必ずしもそうとは限りませんが）モデルの精度に悪影響を与えることがあります。
そして、いま使用しているデータセットの ARR_DEL15 列のデータ（値を予測する対象）はかなりバランスが悪くなっています。
時間どおりに到着したことを表すデータと遅れて到着したことを表すデータの比率は6対1を超えています。

![画像：The ARR_DEL15 column]()

データサイエンティストはこのバランスの悪さを解決するために2つのテクニックを使用します。
ひとつは、アップサンプリングです。
アップサンプリングは、少数派のクラスのサンプル数を増やします。
今回の場合は、遅れて到着する行を追加します。
もうひとつは、ダウンサンプリングです。
ダウンサンプリングは、逆に多数派のクラスのサンプル数を減らします。

今回は、データセットのバランスの悪さを解決するための方法が3つあります。
- 時間どおりに到着したことを表す行の数を減らす。
- <b>BigFlightData.csv</b> にある大きなデータセットから行をインポートすることで、遅れて到着したことを表す行の数を増やす。（ただし、既に存在する行を複製しないように注意してください。または、[Remove Duplicate Rows](https://msdn.microsoft.com/library/azure/dn905805.aspx) モジュールを使用して重複する行を削除してください。）
- [SMOTE(Synthetic Minority Oversampling Technique)](https://www.jair.org/media/953/live-953-2037-jair.pdf) を使用して遅れて到着したことを表す行の数を増やす。

Azure Machine Learning の [SMOTE](https://msdn.microsoft.com/library/azure/dn913076.aspx) モジュールは、最近傍法を使用して少数派のサンプル数を合成的に増やします。
バランスの悪さを解消するために SMOTE を使用するモデルは訓練に時間がかかりますが、SMOTE を使用しないモデルよりも良い結果が得られることがあります。

SMOTE を導入する場合は、それを Split Data モジュールの後ろにあるモデルに追加して、訓練データにのみ影響を与えるように注意してください。
そうしないと、テストデータに合成された行が含まれ、誤った（不正確な）AUCの値になる可能性があります。

## ヒント4：出発予定時刻をビニングする
使用しているデータセットの CRS_DEP_TIME 列は出発予定時刻を表しています。
この列の数値の粒度 （ユニークな値を551個含む）は精度に悪影響を及ぼす可能性があります。
これは[ビニング](http://data-informed.com/enhance-machine-learning-with-standardizing-binning-reducing/)や量子化と呼ばれるテクニックを使用して解決することが可能です。
この列の各数値を100で割り、最も近い整数に切り捨てられた場合はどうなりますか？
1030が10になり、1925が19になるなど、この列に最大24の離散値が残されます。
直感的に言えば、フライトが午前10時30分または午前10時40分に出発するかどうかは問題ではありません。
対して、午前10時30分または午後5時30分に出発するかどうかは大変重要なことです。

![画像：The CRS_DEP_TIME column]()

Azure Machine Learning においてビニングする方法はいくつかあります。
そのうちの1つは、[Group Data Into Bins](https://msdn.microsoft.com/library/azure/dn913065.aspx) モジュールを使用することです。
多くの場合、データサイエンティストは数行の R または Python のコードを書くことを好みます。
そして、これらのコードは [Execute R Script](https://msdn.microsoft.com/library/azure/dn905952.aspx) または [Execute Python Script](https://msdn.microsoft.com/library/azure/dn955437.aspx) を使用してモデルに簡単に組み込むことができます。
CRS_DEP_TIME の値をビニングする簡単な Python のスクリプトを以下に示します。

```python
# df はデータを含むデータフレーム
for index, row in df.iterrows():
    df.loc[index, 'CRS_DEP_TIME'] = math.floor(row['CRS_DEP_TIME'] / 100)
```

このようにして出発時刻をビニングするとモデルの精度が向上する場合は、異なるビンサイズを試してみてもよいでしょう。

## ヒント5：学習アルゴリズムを調整する
Azure Machine Learning の各アルゴリズムは、性能の調整に使用することができるパラメータを公開しています。
[Two-Class Logistic Regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) などのアルゴリズムモジュールをキャンバス上で選択すると、そのパラメータが Properties ペインに表示されます。

![画像：Parameters for Two-Class Logistic Regression]()

異なるパラメータを試すことによってモデルの精度が向上することがありますが、この調整には時間がかかります。
そのため、Azure Machine Learning では [Tune Model Hyperparameters](https://msdn.microsoft.com/library/azure/dn905810.aspx) という名前のモジュールを提供しています。 
[Train Model](https://msdn.microsoft.com/library/azure/dn906044.aspx) を Tune Model Hyperparameters で置き換えることで、訓練に時間をかける代わりに、パラメータの最適な組み合わせを見つけることができます。

> Tune Model Hyperparameters を使用するとき、特に parameter sweep mode を <b>Entire grid</b> に設定すると、訓練にかかる時間が大幅に長くなります。

![画像：Using Tune Model Hyperparameters]()

学習アルゴリズムを調整する方法は Tune Model Hyperparameters だけではありません。
その他のアイデアについては [https://docs.microsoft.com/azure/machine-learning/machine-learning-algorithm-parameters-optimize](https://docs.microsoft.com/azure/machine-learning/machine-learning-algorithm-parameters-optimize) を参照してください。


## ヒント6：より大きいデータセットで訓練する
<b>BigFlightData.csv</b> には、モデルの訓練に使用したデータセットの約4倍のデータセットが含まれています。
より大きなデータセットでモデルを訓練すると、精度が向上することがあります。
ただし、訓練には時間がかかるため、小さなデータセットでモデルを調整してから、より大きなデータセットを導入するとよいでしょう。

目標は<b>0.75以上</b>のAUCの値を達成することでした。
もし達成することができたら、あなたはデータサイエンティストのように考えることを学んだと言えるでしょう！