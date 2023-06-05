---
title: Làm trò: Đảo ngược audio/video với ffmpeg
tags: programming audio-processing ffmpeg
categories: làm-trò
---

## story

Một ngày đẹp trời, khi mình đang dạo bilibili tìm video nhạc Lý Khắc Cần (Hacken) để làm sub thì tóm được video này: Mộng như hoan cựu - Cựu hoan như mộng (夢如歡舊 - 舊歡如夢). Mình vốn chỉ biết có một bài hát được phát hành là Cựu hoan như mộng (舊歡如夢, người ta hay dịch là Mộng đẹp ngày xưa nhưng mình không thích cái tên đó :v) thôi, còn Mộng như hoan cựu thì... Có thể các bạn đã thấy có điều gì đó hơi lạ lạ rồi.

Xem video gốc tại đây:

https://www.bilibili.com/video/BV1LW411Y778/

Lần đầu mình nghe thử, mình chỉ thấy bài đầu rất lạ, còn bài sau tất nhiên là một bài đã rất quen thuộc. Đến khi đọc lời bài hát thì mình mới biết là lời bài đầu tiên chính là đảo ngược lại của lời bài hát Cựu hoan như mộng gốc. Nửa sau của video cũng có chữ "Reverse" hiện trên màn hình LED.

Thế là mình thắc mắc, ~~không biết nhạc có đánh ngược lại không~~ nếu đảo ngược đoạn hát đầu tiên thì kết quả sẽ như thế nào nhỉ =))) và rồi cuộc ~~thử nghiệm~~ làm trò bắt đầu.

## Chuẩn bị đồ chơi

Trước hết, mình cần biết cách đảo ngược video như thế nào. Bạn có thể tìm một website miễn phí nào đó thực hiện đảo ngược video trên mạng, còn mình sử dụng công cụ ffmpeg. Để nói về ffmpeg thì 100% các app/tool xử lý video trên máy tính đều phải cài ffmpeg thì mới hoạt động hiệu quả được (lúc nào đó mình sẽ lên bài về những trò có thể làm với ffmpeg). Các bạn có thể tìm hiểu chi tiết cách cài đặt ffmpeg trên Google.

Để đảo ngược video (chỉ đảo hình, không đảo tiếng), ta gõ thẳng vào cửa sổ dòng lệnh:

```bash
ffmpeg -i <video_filename> -vf reverse <reversed_video_filename>
```

Muốn đảo ngược cả hình cả tiếng, ta thêm `-af areverse`:

```bash
ffmpeg -i <video_filename> -vf reverse -af areverse <reversed_video_filename>
```

`reverse` và `areverse` là các filter để xử lý `<video_filename>` thành `<reverse_video_filename>`. Cơ chế của các reverse filter này là nạp **toàn bộ** audio/video file lên RAM, thế nên đối với những file audio/video nặng, bạn nên chia file thành các đoạn nhỏ ngắn hơn, đảo ngược từng file ngắn và ghép tất cả các file ngắn (đã đảo ngược) đó theo thứ tự ngược lại.

https://video.stackexchange.com/q/17738

Hình dưới là quá ảnh chụp màn hình quá trình mình đảo ngược 1 video gần 5 phút. Video đuôi mp4, khá nhẹ nhưng bộ nhớ mà process ffmpeg sử dụng là hơn 2GB :|

![img]()

## Nghịch dại lần đầu

Trước khi nghịch dại, ta cần cắt đoạn video "hát ngược" gốc đã:

```bash
ffmpeg -i "李克勤 - 梦如欢旧 旧欢如梦 (Live 2011).mp4" -ss 00:32 -to 01:47 "李克勤 - 梦如欢旧.mp4"
```

Thử nghiệm trên bài Mộng như hoan cựu (mình chỉ cắt 1 phần đầu của video Mộng như hoan cựu - Cựu hoan như mộng ở trên), đảo ngược toàn bộ hình và tiếng:

```bash
ffmpeg -i "李克勤 - 梦如欢旧 旧欢如梦 (Live 2011).mp4" -vf vreverse -af areverse -ss 00:01 -to 01:12 "李克勤 - 梦如欢旧 (reverse).mp4"
```

<video width="320" height="240" controls>
  <source src="李克勤 - 梦如欢旧 (reverse).mp4" type="video/mp4">
  Xem trên Google Drive tại đây: [link](link)
</video>

Tiếng video đảo ngược này khá hài hước, đôi chỗ nghe được một vài chữ, nhìn chung không nghe rõ được ca sĩ hát gì, cảm giác như hết hơi vậy =)))

Thử nghiệm trên MV 緩慢 của Beyond, chỉ đảo hình mà không đảo tiếng:

```bash
ffmpeg -i "BEYOND【緩慢】Official Music Video.mp4" -vf reverse "huanman_reverse.mp4"
```

<video width="320" height="240" controls>
  <source src="huanman_reverse.mp4" type="video/mp4">
  Xem trên Google Drive tại đây: [link](link)
</video>

MV gốc xem tại đây:

https://www.youtube.com/watch?v=Oxb40lBURW0

<small>Nói thêm về MV 緩慢, trong đó có một số đoạn là đảo ngược từ cảnh quay thật, một số đoạn là ca sĩ thực hiện hành động ngược lại (ví dụ: đi giật lùi), và có thể có một số đoạn là quay tuyến tính. Mình thực hiện đảo ngược MV này để trải nghiệm =)))</small>

## Suy nghĩ lần 1

Thực ra vốn dĩ Mộng hoan như cựu chỉ là ca sĩ hát ngược lời bài hát gốc, chứ không phải là phát âm ngược lại từng chữ (vì không thể làm được =)))). 

Mình tách phần nhạc nền và vocal thành 2 file audio bằng tool https://vocalremover.org/ (tool AI giờ xịn thật), chịu khó ngồi làm kara cho từng chữ (đơn vị: phần trăm giây):

```text
Dialogue: 0,0:00:01.00,0:00:01.00,Default,,0,0,0,,[rev start]
Dialogue: 0,0:00:16.11,0:00:20.60,Default,,0,0,0,,{\k125}盟{\k67}訂{\k63}共{\k30}縷{\k19}一{\k68}心{\k77}痴
Dialogue: 0,0:00:21.20,0:00:26.28,Default,,0,0,0,,{\k123}翼{\k63}比{\k67}願{\k63}般{\k94}百{\k27}愛{\k71}恩
Dialogue: 0,0:00:28.00,0:00:31.36,Default,,0,0,0,,{\k75}份{\k60}緣{\k65}有{\k71}喜{\k65}共
Dialogue: 0,0:00:32.01,0:00:37.81,Default,,0,0,0,,{\k67}依{\k53}偎{\k79}相{\k54}間{\k47}花{\k62}底{\k218}月
Dialogue: 0,0:00:40.32,0:00:44.81,Default,,0,0,0,,{\k113}任{\k79}信{\k65}互{\k22}印{\k26}相{\k67}心{\k77}心
Dialogue: 0,0:00:45.31,0:00:51.06,Default,,0,0,0,,{\k112}密{\k78}親{\k63}極{\k73}根{\k46}愛{\k71}種{\k132}早
Dialogue: 0,0:00:52.23,0:00:55.43,Default,,0,0,0,,{\k59}近{\k60}情{\k75}性{\k66}家{\k60}大
Dialogue: 0,0:00:56.03,0:01:02.63,Default,,0,0,0,,{\k58}人{\k74}中{\k70}意{\k43}戀{\k84}相{\k73}年{\k258}當
Dialogue: 0,0:01:12.00,0:01:12.00,Default,,0,0,0,,[rev end]
```

rồi viết 1 code Python ngắn để trích ra điểm bắt đầu và điểm kết thúc của từng chữ, sau đó cắt ra từng đoạn audio nhỏ (mỗi audio là cách hát từng chữ, hoặc là khoảng trống, tất cả các đoạn audio liên tục)

*chèn link snippet code Python*

rồi ghép ngược lại thành một file audio không nhạc theo lời bài hát gốc, thành một file audio "đảo ngược của đảo ngược" hoàn chỉnh.

```bash
ffmpeg -f concat -safe 0 -i char_reverse.txt -c copy vocals_reverse_char.mp3
```

Sau khi ghép từ tất cả các file audio "hát" từng chữ thành 1 file `vocals_char_reverse.mp3` hoàn chỉnh, ta cùng nghe thử:

<audio controls>
  <source src="vocals_char_reverse.mp3" type="audio/mpeg">
  Xem trên Google Drive tại đây: [link](link)
</audio>

Có thể nói phiên bản "đảo ngược của đảo ngược" này khá đúng nốt, mặc dù trường độ hơi sai sai =)))

## Nghịch dại lần 2

Lần này mình đảo ngược phần nhạc nền và chồng với file "đảo ngược của đảo ngược". Vì chưa biết cách chồng 2 file audio vào với nhau nên mình mở audio bằng Audacity, giảm bớt âm lượng của phần nhạc nền (tại tool AI kia) rồi export ra file cuối cùng.

<audio controls>
  <source src="vocals_char_music_reverse.mp3" type="audio/mpeg">
  Xem trên Google Drive tại đây: [link](link)
</audio>

## Thu dọn đồ chơi

Tất nhiên mục đích của làm trò là để vui, nhưng bên cạnh đó mình cũng khám phá thêm sức mạnh của ffmpeg, giúp mình có thể quăng bớt vài app xử lý video đi để chuyển qua dùng ffmpeg trên command line =)))

Ngoài ra, những câu chuyện bên lề khác như làm kara hay xử lý cắt/nối file audio/video rất tốn thời gian. Hi vọng sau này có thêm nhiều người "sa" vào thú chơi tốn thời gian này.
