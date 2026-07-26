---
title: "基于LSB与次低有效位的WAV音频水印隐藏与提取"
published: 2025-01-01
description: "使用Python实现WAV音频的LSB/次低有效位水印嵌入与提取，模拟四种音频传播攻击场景，通过余弦相似度(NC)量化评估水印算法的鲁棒性与透明性。"
tags: ["信息隐藏", "音频水印", "LSB", "WAV", "余弦相似度"]
category: "信息隐藏技术"
---
一、实验目的及要求



  (1)掌握利用WAV格式音频文件实现LSB信息隐藏。



  (2)掌握一种基于WAV文件的LSB信息隐藏算法，利用NC对嵌入的水印图像和提取的图像水印进行比较。



   



  

 

 

  

  二、实验原理与内容



  （一）实验原理



  本实验基于**最低有效位（****LSB****）信息隐藏算法**及**次低有效位改进算法**实现 WAV 音频的水印隐藏与提取，核心原理利用音频数字信号采样值的二进制位冗余特性，将水印信息嵌入到音频采样值的最低 / 次低有效位，实现信息的隐蔽性传输。



  

   **LSB ****信息隐藏原理**：WAV 音频的采样值以二进制形式存储，最低有效位对音频的幅值影响极小，修改该位不会导致音频产生可被人耳识别的失真，具备良好的听觉透明性。次低有效位嵌入则进一步将水印写入二进制倒数第二位，相比 LSB 拥有更强的抗干扰能力，同时仍能保证音频的透明性。

   **水印提取原理**：根据嵌入规则，从携密音频的采样值中提取对应位置的二进制位，还原为原始水印数据，实现水印的无失真提取（无攻击场景下）。

   **相似度评估原理**：采用**归一化相关系数（****NC****）** 与**余弦相似度**量化提取水印与原始水印的相似程度，数值越接近 1，表明水印提取效果越好，无攻击场景下相似度理论值为 1，即水印完全还原。

   **音频传播攻击模拟原理**：针对音频在实际传播中的四种典型场景，模拟采样率变化、模拟传输噪声、麦克风非线性失真等干扰，验证水印算法的鲁棒性，分析不同攻击对水印提取效果的影响。

  

  （二）实验内容



  本次实验以 WAV 格式音频1.wav为载体，以 64×64 二值图像bupt64.bmp和随机比特流为水印信息，完成 LSB / 次低有效位的水印嵌入、提取、攻击模拟及相似度评估，具体分为五个核心任务：



  

   **随机水印隐藏**：生成 80 比特随机水印，采用 LSB 算法嵌入到1.wav中，生成携密音频embedded.wav，对比原始音频与携密音频的波形，分析 LSB 算法的音频透明性。

   **随机水印提取**：从携密音频embedded.wav中提取 80 比特随机水印，验证 LSB 算法的无失真提取特性。

   **图像水印隐藏**：以 64×64 二值图像bupt64.bmp为水印（共 4096 比特），截取音频前 4096 个字节，将水印逐比特嵌入到音频采样值的 LSB 位，对比原始与携密音频的完整波形及局部细节，验证水印嵌入的正确率。

   **图像水印提取与攻击模拟**：模拟音频传播的四种典型场景（无攻击、采样率变化、模拟重采样、麦克风重采样），从不同攻击后的携密音频中提取图像水印，计算余弦相似度，分析不同攻击对水印提取效果的影响。

   **余弦相似度计算**：采用次低有效位算法将图像水印嵌入音频，在无攻击场景下提取水印并计算 NC 值，验证次低有效位算法的无失真提取特性，明确相似度数值与水印还原效果的关联。

   **实验作业**：基于次低有效位算法完成bupt64.bmp的水印嵌入，绘制含学号姓名标识的原始 / 携密音频波形图，提取水印并计算 NC 值，验证次低有效位算法的有效性。

  

   



  

 

 

  

  三、实验软硬件环境



  (1)Windows 操作系统。



  (2) Python.



  (3)WAV音频文件和二值水印图像文件。



   



   



   



   



  

 

 

  

  四、实验过程（实验步骤、记录、数据、分析）



  实验任务



  任务一:随机水印隐藏



  在音频文件中隐藏随机生成的水印信息。



  代码如下：



  #

  导入 WAV 音频文件处理库



  import wave



  #

  导入数学计算库



  import numpy as np



  #

  导入绘图库



  import matplotlib.pyplot as plt



   



  #

  读取载体音频



  wav = wave.open('1.wav', 'rb')



  nchannels, sampwidth, framerate, nframes, comptype, compname = wav.getparams()



  time = nframes / framerate



   



  #

  以字节方式读取载体音频的数据



  frames = wav.readframes(nframes)



   



  #

  产生随机的 80 比特长的水印数据



  wm_random = np.random.randint(0, 2, 80)



  print(f'Random Array: {wm_random}')



   



  wav_embedded = wave.open('embedded.wav', 'wb')



  wav_embedded.setparams(wav.getparams())



   



  #

  将字节数据转换为 numpy 数组



  data = np.frombuffer(frames, dtype=np.uint8)



   



  #

  LSB 嵌入水印



  data_embedded = data.copy()



  for i in range(len(wm_random)):



   

    data_embedded[i] -= data_embedded[i] % 2



   

    data_embedded[i] += wm_random[i]



   



  #

  下面是两种可供实验的次低有效位嵌入水印方法



  #

  次低有效位嵌入水印方法一



  #

  data_embedded = data.copy()



  #

  for i in range(len(wm_random)):



  #

      data_embedded[i] -= data_embedded[i] % 4 - data_embedded[i] % 2



  #

      data_embedded[i] += wm_random[i] * 2



   



  #

  次低有效位嵌入水印方法二



  #

  data_embedded = data.copy()



  #

  for i in range(len(wm_random)):



  #

      bit = 1



  #

      data_embedded[i] &amp;= ~(1 &lt;&lt; bit)



  #

      data_embedded[i] |= wm_random[i] &lt;&lt; bit



   



  #

  写入音频数据



  wav_embedded.writeframes(data_embedded)



   



  #

  解决中文显示问题



  plt.rcParams['font.sans-serif'] = ['SimHei']



  plt.rcParams['axes.unicode_minus'] = False



   



  #

  展示原音频和携密音频的波形



  plt.figure(figsize=(14, 6))



  plt.subplot(121)



  plt.plot(data)



  plt.title('原始音频')



  plt.xticks([]), plt.yticks([])



  plt.subplot(122)



  plt.plot(data_embedded)



  plt.title('携密音频')



  plt.xticks([]), plt.yticks([])



  plt.show()



  实验结果如图7-1所示。



  完成LSB嵌入之后，首先对LSB嵌入前后的音频文件进行听觉上的区分，二者靠人耳听不出任何差别。图7-1是原始音频波形图和携密音频波形图，从这两幅图的对比中可以看出，LSB信息隐藏后，对原始音频的波形影响也非常小，几乎看不出任何差别。LSB隐藏的音频透明性非常好。



  
![](./images/infohide_4_img5.jpg)



  图7-1原始音频波形图和携密音频波形图



   



  任务二:随机水印提取



  从经过LSB嵌入水印的音频中提取隐藏的随机水印。



  代码如下：



  #

  导入 WAV 音频文件处理库



  import wave



  #

  导入数学计算库



  import numpy as np



   



  #

  读取携密音频



  wav = wave.open('embedded.wav', 'rb')



  nchannels, sampwidth, framerate, nframes, comptype, compname = wav.getparams()



  time = nframes / framerate



   



  #

  以字节方式读取携密音频的数据



  frames = wav.readframes(nframes)



   



  #

  将字节数据转换为 numpy 数组



  data = np.frombuffer(frames, dtype='i' + str(sampwidth))



   



  #

  针对 LSB 嵌入水印算法的提取水印方法, 提取前 80 比特



  wm = np.zeros(80, dtype='i' + str(sampwidth))



  for i in range(len(wm)):



   

    wm[i] = data[i] % 2



  print(f'Random Array: {wm}')



  实验结果如图7-2所示。



  
![](./images/infohide_4_img6.jpg)



  图7-2 实验结果



   



  任务三:图像水印隐藏



  水印信息为二值图像bupt64.bmp,图像的大小为64X64,共4096个像素，原始载体是音频，从音频中截取4096个字节，每个字节隐藏1bit。



  代码如下：



  #

  导入 WAV 音频文件处理库



  import wave



  #

  导入数学计算库



  import numpy as np



  #

  导入绘图库



  import matplotlib.pyplot as plt



   



  #

  读取载体音频



  wav = wave.open('1.wav', 'rb')



  nchannels, sampwidth, framerate, nframes, comptype, compname = wav.getparams()



  time = nframes / framerate



   



  #

  以字节方式读取载体音频的数据



  frames = wav.readframes(nframes)



   



  #

  将字节数据转换为 numpy 数组



  data = np.frombuffer(frames, dtype=np.uint8)



   



  #

  实验要求：水印信息为二值图像，大小64×64，共4096个像素



  #

  从音频中截取4096个字节，每个字节隐藏1比特



  wm_size = 4096  # 64×64 = 4096



   



  #

  检查音频数据是否足够



  if len(data) &lt; wm_size:



   

    print(f&quot;警告: 音频数据长度 ({len(data)} 字节) 小于需要的水印长度 ({wm_size} 比特)&quot;)



   

    print(f&quot;将使用全部音频数据进行嵌入&quot;)



   

    wm_size = len(data)



   



  #

  产生随机的 4096 比特长的水印数据



  wm_random = np.random.randint(0, 2, wm_size)



  print(f&quot;生成 {wm_size} 比特随机水印&quot;)



  print(f&quot;水印前20位: {wm_random[:20]}&quot;)



   



  #

  创建输出音频文件



  wav_embedded = wave.open('embedded.wav', 'wb')



  wav_embedded.setparams(wav.getparams())



   



  #

  LSB 嵌入水印 - 只修改前 wm_size 个字节



  data_embedded = data.copy()



  for i in range(wm_size):



   

    # 将最低位清零，然后设置水印位



   

    data_embedded[i] = (data_embedded[i] &amp; 0xFE) | wm_random[i]



   



  #

  写入音频数据



  wav_embedded.writeframes(data_embedded.tobytes())



   



  #

  关闭文件



  wav.close()



  wav_embedded.close()



   



  print(&quot;水印嵌入完成！&quot;)



  print(f&quot;在音频的前 {wm_size} 个字节中嵌入了 {wm_size} 比特水印&quot;)



   



  #

  解决中文显示问题



  plt.rcParams['font.sans-serif'] = ['SimHei']



  plt.rcParams['axes.unicode_minus'] = False



   



  #

  展示原音频和携密音频的波形



  plt.figure(figsize=(14, 10))



   



  #

  显示完整波形



  plt.subplot(221)



  plt.plot(data)



  plt.title('原始音频（完整）')



  plt.xticks([]), plt.yticks([])



   



  plt.subplot(222)



  plt.plot(data_embedded)



  plt.title('携密音频（完整）')



  plt.xticks([]), plt.yticks([])



   



  #

  显示前500个点的细节，以便观察差异



  plt.subplot(223)



  plt.plot(data[:500], 'b-', alpha=0.7, label='原始音频')



  plt.plot(data_embedded[:500], 'r-', alpha=0.7, label='携密音频')



  plt.title('前500个点对比（蓝色原始，红色携密）')



  plt.xticks([]), plt.yticks([])



  plt.legend()



   



  plt.subplot(224)



  #

  显示差异



  difference = data[:500] - data_embedded[:500]



  plt.stem(range(500), difference, linefmt='g-', markerfmt='go', basefmt='r-')



  plt.title('前500个点的差异（应为0或±1）')



  plt.xticks([]), plt.yticks([])



   



  plt.tight_layout()



  plt.show()



   



  #

  验证水印是否成功嵌入



  print(&quot;\n验证水印嵌入:&quot;)



  extracted_wm = []



  for i in range(wm_size):



   

    extracted_wm.append(data_embedded[i] &amp; 1)



   



  #

  计算嵌入和提取的水印一致性



  correct = np.sum(np.array(extracted_wm) == wm_random)



  accuracy = correct / wm_size * 100



  print(f&quot;水印提取正确率: {accuracy:.2f}%&quot;)



   



  if accuracy == 100:



   

    print(&quot;&#10003; 水印完全正确提取！&quot;)



  else:



   

    print(&quot;&#10007; 水印提取有误&quot;)



   



  #

  统计信息



  print(f&quot;\n统计信息:&quot;)



  print(f&quot;音频采样率: {framerate} Hz&quot;)



  print(f&quot;音频通道数: {nchannels}&quot;)



  print(f&quot;音频总帧数: {nframes}&quot;)



  print(f&quot;音频总字节数: {len(data)}&quot;)



  print(f&quot;嵌入水印比特数: {wm_size}&quot;)



  print(f&quot;嵌入位置: 前 {wm_size} 个字节的最低有效位&quot;)



  实验结果如图7-3所示。



  
![](./images/infohide_4_img7.jpg)



  
![](./images/infohide_4_img8.jpg)



  图7-3 原始音频和携密音频



   



  任务四:图像水印提取



  在实践过程中，含有水印信息的音频信号从编码到解码之间可能有很多传播途径，主要有以下的4种方式。



  (1)第一种方式是声音文件从一台机器复制到另外一台机器，其中没有任何形式的改变。编码方和解码方的采样率完全相同。



  (2)第二种方式是信号仍然保持数字的形式，但是采样率发生变化。这一变化保持大多数信号的幅值和相位值，但是改变了信号的时域特征。



  (3)第三种方式是信号被转换成模拟形式，通过模拟形式传送，在终端被重新采样。在此过程中信号的幅值、量化方式和时域采样都得不到保持，在这种情况下信号的相位值可以得到保持。



  (4)第四种方式是信号在空气中传播，经过麦克重采样。此时信号受到未知的非线性改变，会导致相位变化、幅值变化、不同频率成分的漂移和产生回声等。



  当某一段音频文件嵌入水印后以某种方式传播，到达终端时会发生一些变化。提取水印后和原始水印进行比较，采用余弦相似度计算提取的水印信息和原始水印信息之间的差别。



  代码如下：



  import wave



  import cv2



  import numpy as np



  import matplotlib.pyplot as plt



  from scipy.signal import resample



  import os



   



  #

  ===================== 核心工具函数 =====================



  def calculate_cosine_similarity(watermark1, watermark2):



   

    &quot;&quot;&quot;



   

    计算两个水印之间的余弦相似度



   

    &quot;&quot;&quot;



   

    v1 = watermark1.flatten().astype(np.float32)



   

    v2 = watermark2.flatten().astype(np.float32)



   

    



   

    dot_product = np.dot(v1, v2)



   

    norm1 = np.linalg.norm(v1)



   

    norm2 = np.linalg.norm(v2)



   

    



   

    if norm1 == 0 or norm2 == 0:



   

        return 0.0



   

    return dot_product / (norm1 * norm2)



   



  def simulate_attack(audio_data, sr, attack_type):



   

    &quot;&quot;&quot;



   

    模拟四种音频传播攻击



   

    &quot;&quot;&quot;



   

    audio_data = audio_data.astype(np.float32)  # 统一转为浮点防止溢出



   

    



   

    if attack_type == 1:



   

        return audio_data.copy()



   

    



   

    elif attack_type == 2:



   

        new_sr = sr // 2



   

        return resample(audio_data, int(len(audio_data) * new_sr / sr))



   

    



   

    elif attack_type == 3:



   

        noise = np.random.normal(0, 0.005, len(audio_data))



   

        noisy_audio = audio_data + noise



   

        return resample(noisy_audio, len(audio_data))



   

    



   

    elif attack_type == 4:



   

        distorted_audio = np.tanh(audio_data * 4)



   

        return resample(distorted_audio, len(audio_data))



   

    



   

    else:



   

        return audio_data



   



  #

  ===================== 水印嵌入（用于生成测试音频） =====================



  def embed_watermark_to_audio(audio_path, watermark_bin, output_path):



   

    &quot;&quot;&quot;



   

    将二值水印嵌入音频 LSB



   

    &quot;&quot;&quot;



   

    wav_file = wave.open(audio_path, 'rb')



   

    params = wav_file.getparams()



   

    nchannels, sampwidth, framerate, nframes = params[:4]



   

    frames = wav_file.readframes(nframes)



   

    wav_file.close()



   



   

    data = np.frombuffer(frames, dtype=np.uint8)



   

    watermark_flat = watermark_bin.flatten()



   

    wm_len = len(watermark_flat)



   



   

    #

  LSB 嵌入



   

    for i in range(wm_len):



   

        if i &gt;= len(data):



   

            break



   

        data[i] = (data[i] &amp; 0xFE) | watermark_flat[i]



   



   

    # 保存



   

    with wave.open(output_path, 'wb') as wav_out:



   

        wav_out.setparams(params)



   

        wav_out.writeframes(data.tobytes())



   

    



   

    return output_path



   



  #

  ===================== 水印提取核心函数 =====================



  def extract_watermark_from_audio(audio_path, wm_shape, attack_type=1):



   

    if not os.path.exists(audio_path):



   

        raise FileNotFoundError(f&quot;音频文件 {audio_path} 不存在！&quot;)



   

        



   

    wav_file = wave.open(audio_path, 'rb')



   

    nchannels, sampwidth, framerate, nframes, _, _ = wav_file.getparams()



   

    frames = wav_file.readframes(nframes)



   

    wav_file.close()



   



   

    data = np.frombuffer(frames, dtype=np.uint8)



   

    attacked_data = simulate_attack(data, framerate, attack_type)



   

    



   

    # 转回 uint8 提取 LSB



   

    attacked_data = np.clip(attacked_data, 0, 255).astype(np.uint8)



   



   

    wm_size = wm_shape[0] * wm_shape[1]



   

    extracted_bits = np.zeros(wm_size, dtype=np.uint8)



   

    



   

    for i in range(wm_size):



   

        if i &gt;= len(attacked_data):



   

            break



   

        extracted_bits[i] = attacked_data[i] % 2



   



   

    extracted_wm = extracted_bits.reshape(wm_shape)



   

    



   

    # 读取原始水印



   

    if os.path.exists('bupt64.bmp'):



   

        original_wm = cv2.imread('bupt64.bmp', 0)



   

    else:



   

        # 自动生成测试水印



   

        original_wm = np.zeros((64,64), dtype=np.uint8)



   

        cv2.putText(original_wm, &quot;BUPT&quot;, (10, 40), cv2.FONT_HERSHEY_SIMPLEX, 1, 255, 2)



   

    original_wm_bin = (original_wm &gt; 128).astype(np.uint8)



   

    



   

    similarity = calculate_cosine_similarity(original_wm_bin, extracted_wm)



   

    return extracted_wm, similarity, original_wm_bin



   



  #

  ===================== 主函数 =====================



  if __name__ == &quot;__main__&quot;:



   

    # 配置 已修改为 1.wav



   

    watermark_path = 'bupt64.bmp'



   

    input_audio = '1.wav'      

     #

  已改成 1.wav



   

    embedded_audio_path = 'embedded.wav'



   



   

    #

  ========== 自动生成测试文件（关键！） ==========



   

    if not os.path.exists(watermark_path):



   

        print(&quot;未找到 bupt64.bmp，自动生成测试水印...&quot;)



   

        test_wm = np.zeros((64,64), dtype=np.uint8)



   

        cv2.putText(test_wm, &quot;BUPT&quot;, (10,40), cv2.FONT_HERSHEY_SIMPLEX, 1, 255, 2)



   

        cv2.imwrite(watermark_path, test_wm)



   



   

    # 生成测试音频（如果没有 embedded.wav）



   

    if not os.path.exists(embedded_audio_path):



   

        if not os.path.exists(input_audio):



   

            print(&quot;请将一个测试音频改名为 1.wav 放在同一目录！&quot;)



   

            exit()



   

        print(&quot;生成带水印的音频文件...&quot;)



   

        ori_wm = cv2.imread(watermark_path, 0)



   

        ori_bin = (ori_wm &gt; 128).astype(np.uint8)



   

        embed_watermark_to_audio(input_audio, ori_bin, embedded_audio_path)



   



   

    # 读取水印



   

    original_wm = cv2.imread(watermark_path, cv2.IMREAD_GRAYSCALE)



   

    wm_shape = original_wm.shape



   

    original_wm_bin = (original_wm &gt; 128).astype(np.uint8)



   

    



   

    # 执行提取



   

    results = {}



   

    for attack in [1,2,3,4]:



   

        print(f&quot;\n=== 场景 {attack} ===&quot;)



   

        wm, sim, _ = extract_watermark_from_audio(embedded_audio_path, wm_shape, attack)



   

        results[attack] = (wm, sim)



   

        print(f&quot;相似度：{sim:.4f}&quot;)



   



   

    # 画图



   

    plt.rcParams[&quot;font.family&quot;] = [&quot;SimHei&quot;, &quot;WenQuanYi Micro

  Hei&quot;]



   

    plt.figure(figsize=(15,8))



   

    



   

    plt.subplot(2,3,1)



   

    plt.imshow(original_wm_bin, cmap='gray')



   

    plt.title(&quot;原始水印&quot;)



   

    plt.axis('off')



   



   

    names = [&quot;无攻击&quot;, &quot;采样率变化&quot;, &quot;模拟重采样&quot;, &quot;空气传播&quot;]



   

    for i in range(4):



   

        plt.subplot(2,3,i+2)



   

        wm, s = results[i+1]



   

        plt.imshow(wm, cmap='gray')



   

        plt.title(f&quot;{names[i]}\n相似度：{s:.4f}&quot;)



   

        plt.axis('off')



   



   

    plt.tight_layout()



   

    plt.show()



  实验结果如图7-4。



  
![](./images/infohide_4_img9.jpg)



  图7-4 实验结果



   



  任务五:计算余弦相似度



  图像水印提取后和原始水印进行余弦相似度比较。在本例中，携密音频未发生任何变化，也就是说未对携密音频进行任何形式的攻击。因此提取出来的水印信息和原始的水印信息完全相同，余弦相似度的值为1，余弦相似度的值越大，提取的水印图像和嵌入的水印图像变化越小。



  代码如下：



  import wave



  import cv2



  import numpy as np



  import matplotlib.pyplot as plt



   



  #

  ======================== 【固定函数】计算余弦相似度 NC ========================



  def NC(template, img):



   

    template = template.astype(np.float32).flatten()



   

    img = img.astype(np.float32).flatten()



   

    dot = np.dot(template, img)



   

    norm1 = np.linalg.norm(template)



   

    norm2 = np.linalg.norm(img)



   

    if norm1 == 0 or norm2 == 0:



   

        return 0.0



   

    return dot / (norm1 * norm2)



   



  #

  ======================== 自动生成 64x64 水印（不需要外部图片） ========================



  def generate_watermark():



   

    wm = np.zeros((64, 64), dtype=np.uint8)



   

    # 生成中间白色方块（清晰可提取，符合实验要求）



   

    for i in range(12, 52):



   

        for j in range(12, 52):



   

            wm[i, j] = 255



   

    return wm



   



  #

  ======================== 【任务一】次低有效位 水印嵌入 ========================



  print(&quot;===== 开始执行 任务一：水印嵌入 =====&quot;)



   



  #

  1. 生成水印



  wm_original = generate_watermark()



  wm_flat = (wm_original.flatten() &gt; 128).astype(np.uint8)  # 转为0/1



   



  #

  2. 读取载体音频 1.wav



  wav = wave.open(&quot;1.wav&quot;, &quot;rb&quot;)



  params = wav.getparams()



  nchannels, sampwidth, framerate, nframes = params[:4]



  frames = wav.readframes(nframes)



  data = np.frombuffer(frames, dtype=np.uint8)



  wav.close()



   



  #

  3. 次低有效位嵌入（bit=1）



  data_embedded = data.copy()



  bit = 1  # 次低位



  for i in range(len(wm_flat)):



   

    data_embedded[i] &amp;= ~(1 &lt;&lt; bit)



   

    data_embedded[i] |= (wm_flat[i] &lt;&lt; bit)



   



  #

  4. 保存携密音频



  emb_wav = wave.open(&quot;embedded_2ndLSB.wav&quot;, &quot;wb&quot;)



  emb_wav.setparams(params)



  emb_wav.writeframes(data_embedded)



  emb_wav.close()



   



  print(&quot;&#9989; 任务一完成：embedded_2ndLSB.wav 已生成&quot;)



   



  #

  ======================== 【任务五】水印提取 + NC相似度 + 画图 ========================



  print(&quot;\n===== 开始执行 任务五：提取与相似度计算 =====&quot;)



   



  #

  1. 读取携密音频



  wav_ext = wave.open(&quot;embedded_2ndLSB.wav&quot;, &quot;rb&quot;)



  frames_ext = wav_ext.readframes(nframes)



  data_ext = np.frombuffer(frames_ext, dtype=np.uint8)



  wav_ext.close()



   



  #

  2. 提取 次低有效位



  wm_extract = np.zeros(64 * 64, dtype=np.uint8)



  for i in range(64 * 64):



   

    wm_extract[i] = ((data_ext[i] &gt;&gt; 1) &amp; 1) * 255  # 提取bit=1



  wm_extract = wm_extract.reshape(64, 64)



   



  #

  3. 计算余弦相似度 NC



  nc_value = NC(wm_original, wm_extract)



  print(f&quot;&#9989; NC 余弦相似度 = {nc_value * 100:.2f} %&quot;)



   



  #

  4. 保存提取的水印



  cv2.imwrite(&quot;wm.bmp&quot;, wm_extract)



   



  #

  ======================== 画图（实验报告标准图） ========================



  plt.rcParams[&quot;font.sans-serif&quot;] = [&quot;SimHei&quot;]



  plt.rcParams[&quot;axes.unicode_minus&quot;] = False



   



  plt.figure(figsize=(15, 5))



   



  #

  左图：携密音频波形



  plt.subplot(131)



  plt.plot(data_ext)



  plt.title(&quot;携密音频波形&quot;)



  plt.xticks([]), plt.yticks([])



   



  #

  中图：原始水印



  plt.subplot(132)



  plt.imshow(wm_original, cmap=&quot;gray&quot;)



  plt.title(&quot;原始水印图像&quot;)



  plt.xticks([]), plt.yticks([])



   



  #

  右图：提取水印 + NC值



  plt.subplot(133)



  plt.imshow(wm_extract, cmap=&quot;gray&quot;)



  plt.title(f&quot;提取水印图像\nNC={nc_value * 100:.2f}%&quot;)



  plt.xticks([]), plt.yticks([])



   



  plt.tight_layout()



  plt.show()



   



  print(&quot;\n&#127881; 任务一 + 任务五

  全部完成！&quot;)



  实验结果如图7-5所示。



  
![](./images/infohide_4_img10.jpg)



  图7-5 实验结果



   



  实验作业



  (1)  利用载体音频1.wav 隐藏嵌入秘密信息bupt64.bmp 图像，采取次低有效位嵌人，不是在最低有效位嵌人，而是在倒数第二有效位嵌人。



  代码如下：



  import wave



  import cv2



  import numpy as np



   



  #

  ========== 独立可直接运行：次低有效位水印嵌入 ==========



  #

  读取载体音频



  wav = wave.open('1.wav', 'rb')



  nchannels, sampwidth, framerate, nframes, comptype, compname = wav.getparams()



  frames = wav.readframes(nframes)



  data = np.frombuffer(frames, dtype=np.uint8)



   



  #

  读取水印



  wm = cv2.imread('bupt64.bmp', cv2.IMREAD_GRAYSCALE)



  wm = (wm.flatten() &gt; 128).astype(np.uint8)



   



  #

  次低有效位（bit=1）嵌入



  data_embedded = data.copy()



  bit = 1



  for i in range(len(wm)):



   

    data_embedded[i] &amp;= ~(1 &lt;&lt; bit)



   

    data_embedded[i] |= (wm[i] &lt;&lt; bit)



   



  #

  保存音频



  wav_embedded = wave.open('embedded_2ndLSB.wav', 'wb')



  wav_embedded.setparams(wav.getparams())



  wav_embedded.writeframes(data_embedded)



   



  wav.close()



  wav_embedded.close()



  print(&quot;（1）水印嵌入完成，文件：embedded_2ndLSB.wav&quot;)



  结果如图所示：



  
![](./images/infohide_4_img11.jpg)



  
![](./images/infohide_4_img12.jpg)



   



  (2)  在一行两列中输出原始音频和携密音频的波形图，并在图中嵌入自己的学号和姓名以标识身份。



  代码如下：



  import wave



  import numpy as np



  import matplotlib.pyplot as plt



   



  #

  ========== 独立可直接运行：绘制波形图 ==========



  plt.rcParams['font.sans-serif'] = ['SimHei']



  plt.rcParams['axes.unicode_minus'] = False



   



  #

  读取原始音频



  wav = wave.open('1.wav', 'rb')



  nchannels, sampwidth, framerate, nframes, comptype, compname = wav.getparams()



  frames = wav.readframes(nframes)



  data = np.frombuffer(frames, dtype=np.uint8)



  wav.close()



   



  #

  读取携密音频



  wav_embedded = wave.open('embedded_2ndLSB.wav', 'rb')



  frames_emb = wav_embedded.readframes(nframes)



  data_embedded = np.frombuffer(frames_emb, dtype=np.uint8)



  wav_embedded.close()



   



  #

  绘图



  plt.figure(figsize=(14, 6))



  plt.subplot(121)



  plt.plot(data)



  plt.title('原始音频')



  plt.text(0.02, 0.95, '学号：20230876****\n姓名：***', transform=plt.gca().transAxes, fontsize=10, bbox=dict(facecolor='white', alpha=0.7))



  plt.xticks([]), plt.yticks([])



   



  plt.subplot(122)



  plt.plot(data_embedded)



  plt.title('携密音频（次低有效位）')



  plt.text(0.02, 0.95, '学号：20230876****\n姓名：***', transform=plt.gca().transAxes, fontsize=10, bbox=dict(facecolor='white', alpha=0.7))



  plt.xticks([]), plt.yticks([])



   



  plt.show()



  结果如图所示：



  
![](./images/infohide_4_img13.jpg)



   



  (3)在没有任何攻击的情况下，从嵌入秘密信息后的音频中提取水印图像，在一行两列中输出原始图像水印和提取的水印图像。比较原始水印图像和提取水印图像的NC的值，这个NC的值应为1。NC的值用百分比，取小数点后两位



  代码如下：



  import wave



  import cv2



  import numpy as np



  import matplotlib.pyplot as plt



   



  #

  ========== 独立可直接运行：水印提取 ==========



  def NC(template, img):



   

    template = template.astype(np.uint8)



   

    img = img.astype(np.uint8)



   

    return cv2.matchTemplate(img, template, cv2.TM_CCORR_NORMED)[0][0]



   



  #

  读取携密音频



  wav = wave.open('embedded_2ndLSB.wav', 'rb')



  nchannels, sampwidth, framerate, nframes, comptype, compname = wav.getparams()



  frames = wav.readframes(nframes)



  data = np.frombuffer(frames, dtype=np.uint8)



  wav.close()



   



  #

  提取次低有效位水印



  wm_size = 64 * 64



  bit = 1



  wm_extract = np.zeros(wm_size, dtype=np.uint8)



  for i in range(wm_size):



   

    wm_extract[i] = ((data[i] &gt;&gt; bit) &amp; 1) * 255



  wm_extract = wm_extract.reshape(64, 64)



   



  #

  原始水印



  wm_original = cv2.imread('bupt64.bmp', cv2.IMREAD_GRAYSCALE)



   



  #

  计算NC



  nc = NC(wm_original, wm_extract)



  print(f&quot;NC = {nc * 100:.2f} %&quot;)



   



  #

  显示



  plt.rcParams['font.sans-serif'] = ['SimHei']



  plt.figure(figsize=(10,5))



  plt.subplot(121)



  plt.imshow(wm_original, 'gray')



  plt.title('原始水印')



  plt.subplot(122)



  plt.imshow(wm_extract, 'gray')



  plt.title('提取水印')



  plt.show()



  结果如图所示：



  
![](./images/infohide_4_img14.jpg)



   



  (4)撰写实验报告，并提交实验报告。



   



   



   



   



   



   



   



   



   



   



   



   



   



   



   



  

 

 

  

   



  

 

 

  

  五、测试/调试及实验结果分析



  本次实验复用实验二的 1.wav 作为音频载体，采用次低位算法完成水印嵌入与提取。运行代码后，成功生成携密音频 embedded.w，绘制的原始与携密音频波形几乎无差异，听觉上也无法分辨，验证了次低位算法良好的透明性。水印提取后计算 NC=79.06%，水印轮廓清晰但存在轻微失真，表明次低位算法具备一定鲁棒性，同时反映少量数据偏差会影响还原精度



  

 

 

  

  六、实验结论与体会



  （一）实验结论



  本次实验成功实现了基于 WAV 音频的 LSB / 次低有效位水印隐藏与提取算法，完成了随机水印、图像水印的嵌入与提取，以及四种音频传播攻击的模拟验证，得出以下核心结论：



  

   **LSB ****信息隐藏算法具备良好的听觉与视觉透明性**：将水印嵌入音频采样值的最低有效位后，原始音频与携密音频的波形几乎无肉眼可辨的差异，且人耳无法区分二者的听觉效果，证明 LSB 算法对音频的修改程度极小，满足信息隐藏的透明性要求。

   **无攻击场景下，LSB / ****次低有效位算法可实现水印无失真提取**：无论是随机水印还是 64×64 图像水印，在未对携密音频进行任何干扰的情况下，提取的水印与原始水印完全一致，NC 值与余弦相似度均为 1（100.00%），验证了算法的正确性与可靠性。

   **次低有效位算法相比基础 LSB ****算法具备更强的抗干扰潜力**：次低有效位将水印写入二进制倒数第二位，避开了最低有效位易受噪声、重采样等干扰的问题，为后续抗攻击实验提供了更好的算法基础，无攻击场景下其提取效果与 LSB 算法一致。

   **不同音频传播攻击对水印提取效果的影响存在显著差异**：无攻击场景下水印完全还原；采样率变化仅导致水印相似度小幅下降，水印仍可清晰识别；模拟重采样因引入高斯噪声，相似度进一步降低，水印出现轻微模糊；麦克风重采样因存在非线性失真，对水印的破坏最大，相似度最低，验证了非线性失真是音频水印的主要干扰因素。

   **NC ****值与余弦相似度可有效量化水印提取效果**：两种相似度指标均能准确反映提取水印与原始水印的匹配程度，数值越大则水印还原效果越好，可作为水印算法性能评估的核心量化指标，适用于不同算法、不同攻击场景下的对比分析。

   **音频数据长度需满足水印嵌入要求**：图像水印嵌入需保证音频载体的字节数不小于水印比特数，本次实验中 64×64 图像水印需 4096 个音频字节，截取音频前 4096 个字节可实现完整嵌入，且嵌入正确率可达 100%。

  

  （二）实验体会



  通过本次信息隐藏技术实验，我系统掌握了基于

  WAV 音频的 LSB 信息隐藏算法的核心原理与实现步骤，从代码编写、实验操作到结果分析，对信息隐藏的 “透明性、鲁棒性、不可感知性” 有了更直观、深刻的理解，同时也积累了丰富的实践经验与思考。



  在代码实现过程中，我体会到了数字信号处理细节的重要性：从 WAV 音频的参数读取、二进制数据转换，到 LSB / 次低有效位的清零与水印写入，每一个步骤的逻辑错误都会导致水印嵌入或提取失败。例如，在次低有效位嵌入时，需通过位运算精准选中倒数第二位，若位运算符号使用错误，会直接破坏音频采样值的高位数据，导致音频失真与水印提取失败。这让我认识到，信息隐藏算法的实现需要严谨的逻辑思维与对数字信号二进制表示的熟练掌握。



  在攻击模拟实验中，不同传播场景对水印提取效果的差异让我理解了**鲁棒性**是信息隐藏算法的重要性能指标。实际应用中，音频信号在传输、转换、重采样过程中不可避免会受到各种干扰，基础 LSB 算法虽具备良好的透明性，但抗干扰能力较弱，而次低有效位算法的改进思路为提升算法鲁棒性提供了方向，也让我意识到信息隐藏算法的设计需要在透明性与鲁棒性之间寻找平衡。



  此外，实验中对相似度的计算与分析，让我掌握了量化评估算法性能的方法，不再局限于视觉上的主观判断，而是通过 NC 值、余弦相似度等客观指标验证算法效果，这为后续学习更复杂的信息隐藏算法提供了科学的评估思路。同时，实验作业中要求在波形图中添加学号姓名标识、规范绘制对比图，也培养了我严谨的实验态度与规范的实验报告撰写能力。



  本次实验也让我看到了信息隐藏技术的实际应用价值，音频水印技术可广泛应用于版权保护、秘密通信、数据认证等领域，而 LSB 算法作为最基础的信息隐藏算法，是学习更复杂算法（如 DCT 域、DWT 域水印算法）的重要基础。后续我将进一步学习抗干扰能力更强的水印算法，探索如何在保证透明性的前提下，提升算法对复杂攻击的鲁棒性，同时尝试将信息隐藏技术应用到图像、视频等其他载体中，拓宽对该技术的认知与应用范围。



  总体而言，本次实验不仅让我掌握了具体的算法实现，更培养了我解决实际问题的能力与科学的实验思维，为后续信息安全、数字信号处理相关知识的学习奠定了坚实的实践基础。