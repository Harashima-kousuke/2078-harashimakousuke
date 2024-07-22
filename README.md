Gmailのフィルタ機能とGoogleスプレッドシートを組み合わせて、Googleフォームからの通知を自動的にまとめる仕組みです。
Googleフォームからの通知をGmailのフィルタを設定してフィルタリングします。
function summarizeFormNotifications() {
  var label = GmailApp.getUserLabelByName("Googleフォーム通知");
  var threads = label.getThreads();
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var summary = "";
  
  // スプレッドシートにデータを追加
  for (var i = 0; i < threads.length; i++) {
    var messages = threads[i].getMessages();
    for (var j = 0; j < messages.length; j++) {
      var message = messages[j];
      var subject = message.getSubject();
      var date = message.getDate();
      var body = message.getPlainBody();
      
      // スプレッドシートに書き込み
      sheet.appendRow([date, subject, body]);
      
      summary += "件名: " + subject + "\n";
      summary += "日付: " + date + "\n";
      summary += "内容: " + body + "\n\n";
    }
  }
  
  // ラベルをクリアして次回の実行に備える
  for (var k = 0; k < threads.length; k++) {
    threads[k].removeLabel(label);
  }
}

// 定期的に実行するトリガーを設定
function createTimeDrivenTriggers() {
  ScriptApp.newTrigger('summarizeFormNotifications')
      .timeBased()
      .everyDays(1)
      .atHour(9)
      .create();
}
このコードをGoogle App scriptに書き込み、一日のある時刻に自動的にgmailから該当するメールを探してGoogkeスプレッドシートに追加します。
