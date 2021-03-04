# SQS(Amazon Simple Queue Service)
メッセージキューを提供するAWSのサービス。
本ドキュメント作成段階の情報のため、情報の陳腐化には注意すること。

## 【Amazon CLI ドキュメント】
公式のドキュメントは[こちら](https://docs.aws.amazon.com/cli/latest/reference/sqs/index.html)を参考にする。

## 【SQSに登場する特に重要な設定値】
### 可視性タイムアウト
1つのコンシューマー(QueueからメッセージをDequeueする何らかのサービスやバッチといったクライアントを指す。)が受信しているメッセージが他のコンシューマーに非表示になっている時間のこと。  
これが設定されることにより、複数のコンシューマーが存在している場合にメッセージが複数回処理されてしまうと現象を防ぐことができる。  
**可視性タイムアウトは、Amazon SQS がメッセージを返すときに開始される。**  
可視性タイムアウトを超えても、メッセージを処理できなかった場合、対象のメッセージは他のコンシューマーにも参照できるようになる。  
つまり、メッセージの受信を１度だけにして、重複処理を抑制したい場合はこの設定だけではなく、 **ここで指定した時間以内に、メッセージを受信して、メッセージを削除までする必要がある。**  


## 【簡易操作方法】
### 1. Queueの作成
📝参考URL: https://docs.aws.amazon.com/cli/latest/reference/sqs/create-queue.html
```sh
# Standard Queue
aws sqs create-queue --queue-name [queue_name] --attributes file://attributes.json

# or

# FIFO Queue(Queue名のSuffixに.fifoが必要でかつ、attributesの"FifoQueue: true"を設定している必要がある。
aws sqs create-queue --queue-name [queue_name].fifo --attributes file://attributes.json
```
```json
// attributes.json
{
    "DelaySeconds": "0",                  // 配信遅延 0秒
    "MaximumMessageSize": "262144",       // 最大メッセージサイズ 256KB
    "MessageRetentionPeriod": "345600",   // メッセージ保持期間 4日間
    "ReceiveMessageWaitTimeSeconds": "1", // メッセージ受信待機時間(ReceiveMessage実行時に到着まで待機している時間) 1秒
    "VisibilityTimeout": "30",            // 可視性タイムアウト 30秒
    "FifoQueue": "true"                   // キュー名にSuffixとして`.fifo`がついている場合にのみ有効。※1
}
```
※1 このプロパティの扱いが非常に面倒なので注意する。2021/4/19現在は、.fifoのSuffixがついている名前でかつ、`"FifoQueue": "true"`の状態でしか設定できない。仮に.fifoなしの名前で、true,falseに限らず、`FifoQueue`を設定すると、プロパティはないよ。と怒られる。逆に.fifoのSuffixをつけた状態で、`"FifoQueue": "false"`をつけると、名前がおかしいというエラーがでる。中々使い勝手が悪いので、今後に改善させる可能性は高い。

### 2. Messageの送信
📝参考URL: https://docs.aws.amazon.com/cli/latest/reference/sqs/send-message.html
```sh
# URLはQueueのURLを指定する。(下記のURLは適当なので注意)
aws sqs send-message --queue-url https://sqs.us-east-1.amazonaws.com/123456789/queue_name --message-body "Hello Message"
```

## 【Q & A】
- Q: メッセージを受信後にメッセージの内容に応じて行った処理が意図せず遅延してしまい、可視性タイムアウト経過後に削除リクエストしてしまった場合は、メッセージが予期せぬタイミングで削除されてしまわないか？
  - A: メッセージ削除に必要な`ReceiptHandle`は、受信したメッセージのBodyに付属しており、このキー配列は可視性タイムアウト時間に有効なユニークな値。つまり、この時間を超えてリクエストしたとしても、そのキー配列が刺すメッセージは存在しないことになるため、不用意な削除は発生しない。




## 【参考・引用資料】
- [AWS CLI Command Reference -sqs-](https://docs.aws.amazon.com/cli/latest/reference/sqs/index.html)