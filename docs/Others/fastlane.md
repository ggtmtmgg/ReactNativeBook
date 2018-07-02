# fastlane
![fastlane](/images/fastlane.png)

fastlaneはAndroidとiOSのベータデプロイとリリースを最も簡単に自動化する方法です。
スクリーンショットを取ることやプロビジョニングプロファイルの更新やリリース作業など、すべての退屈なタスクを自動化します。
Rubyのgemとして提供されているため簡単にインストールすることができます。

actionとlaneという概念があり、複数のactionを1つのフロートしてまとめたlaneを定義して

    $ fastlane <lane name>

といった形で実行します。
