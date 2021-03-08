# Unity

## 【UnityでVSCodeを使用する】

Utnityをそのまま使用しようとすると、DefaultのEditorはVisual Studioが選択させる。  
Visual Studioだと、重かったり、そもそもエディタを変更するのに抵抗がある場合はいろいろと辛いので、VSCodeを使用した方が個人的には良い。
ただ、VSCodeあるあるだが、あくまで拡張という位置づけなことが多いので単純に設定するだけだと、インテリセンスが反応しなかったりで辛い。  
そういったときは下記を参考にする。  

1. Monoがインストールされているか?
   Monoが何であるかは割愛するが、これがないとインテリセンスが反応しない。というかここにPackage群がいるので、こいつがいないとインテリセンスが読み込むための情報がないということになる。なので、まずは`mono`の存在を確認する。

   ```sh
   # Macの場合
   > mono --version
   Mono JIT compiler version 6.12.0.125 (2020-02/8c552e98bd6 Mon Mar  8 13:06:52 EST 2021)
   Copyright (C) 2002-2014 Novell, Inc, Xamarin Inc and Contributors. www.mono-project.com
        TLS:           
        SIGSEGV:       altstack
        Notification:  kqueue
        Architecture:  amd64
        Disabled:      none
        Misc:          softdebug 
        Interpreter:   yes
        LLVM:          yes(610)
        Suspend:       hybrid
        GC:            sgen (concurrent by default)
   ```

   情報が表示されればOK。何も表示されない場合は、`brew install mono`でInstallするか、<https://www.mono-project.com/download/stable/>直接サイトからダウンロードする。

2. OmniSharpの設定が行われているか。
   残念ながら、ただVSCodeをDefaultのエディタに設定するだけでは、設定が行われないらしい。

   ```json
   {
       /* ========== Mono ========== */
       "omnisharp.monoPath": "/Library/Frameworks/Mono.framework/Versions/Current",
       "omnisharp.useGlobalMono": "always",
   }
   ```

   `"omnisharp.monoPath"`には、MonoがInstallされているフォルダを選択する。ここで重要なのは、`/bin`を含んでいるフォルダをRootとして設定すること。  
   上のフォルダ場合は、`Current`ディレクトリ配下に`/bin`フォルダが存在しているので、ここを指定している。  
   `"omnisharp.useGlobalMono"`は`always`に設定している。`autu`に設定している状態で、MonoのVersionUpをするとインテリセンスが聞かないときがあるので、個人的には`always`の設定が無難。