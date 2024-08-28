# soal untuk pretest-data-engineer

## Knowledge Base
1. Apa yang Anda ketahui tentang Data Engineer ?
2. Ceritakan pengalaman Anda dalam mengolah data?
3. Apa yang kamu ketahui tentang AI
4. Mengapa Data menjadi sesuatu yang sangat penting sekarang, dan apa dampak dari kebocoran data
5. Apa yg anda ketahui mengenai model generative AI ? ada apa saja penerapannya

Jawaban
1. Data engineer adalah seseorang yang bertugas untuk membangun sistem untuk mengumpulkan dan mengolah data sehingga bisa digunakan untuk keperluan perusahaan.
2. Saya pernah mengolah data untuk sentimen analisis. selain itu saya pernah mengolah data untuk rekomendasi menggunakan Azure. Saya juga pernah membuat streamlit bank churn berdasarkan data bank churn.
3. AI adalah kepanjangan dari Artificial Intelligence, AI merupakan teknologi yang dirancang untuk membuat sistem komputer mampu meniru kemampuan intelektual manusia.
4. Data memiliki peran penting dalam pengambilan keputusan. Ketersediaan data memungkinkan seseorang membuat keputusan yang lebih baik dalam menghadapi permasalahan tertentu. Salah satu dampak dari kebocoran data adalah Pencurian Identitas. Data pribadi yang bocor dapat digunakan oleh pelaku kejahatan untuk mencuri identitas seseorang. Ini bisa berujung pada pembukaan rekening bank fiktif, aplikasi kredit palsu, dan bahkan kegiatan kriminal lainnya atas nama korban.
5. model generative AI adalah tipe AI yang dapat membuat konten dan ide baru, termasuk percakapan, cerita, gambar, video, dan musik. generative AI adalah langkah selanjutnya dalam kecerdasan buatan. Kita dapat melatihnya untuk belajar bahasa manusia, bahasa pemrograman, seni, kimia, biologi, atau materi pelajaran kompleks apa pun. Misalnya, AI generatif dapat mempelajari kosakata bahasa Inggris dan membuat puisi dari kata-kata yang diprosesnya.
   
## Soal Coding
studi kasus = 
seorang data engineer diharuskan oleh klien untuk membuat
blueprint data orchestration di sebuah tools yaitu Kestra.
klien ingin memproses data review lazada (review-lazada.csv).

kami sudah menyediakan akses kestra di
[Kestra](https://kestra-magang.t-dev.site/) - https://kestra-magang.t-dev.site/ 
silahkan pelajari kestra lihat dokumentasinya 
dan silahkan lakukan proses dibawah ini:

1. satu flows untuk proses data cleansing dimana bersihkan data reviewnya kemudian export ke csv
2. satu flows untuk proses menghitung sentiment analysis berdasarkan kolom rating

Keterangan :
flow kestra dan script python sertakan dalam git kalian ketika pelaporan

Berikut adalah script python saya untuk data cleaning data pada kestra

id: cleaning-data
namespace: pretest2

inputs:
  - id: file
    type: FILE

tasks:
  - id: cleaning
    type: io.kestra.plugin.scripts.python.Script
    inputFiles: 
      input_csv: "{{ inputs.file }}"
    beforeCommands: 
      - pip install pandas
    script: |
      import pandas as pd
      import re

      df = pd.read_csv('{{ inputs.file }}', na_values=['null'])
      df.drop(df.columns[[1,2,4,5,7,8,9,10,11,12,13,14]], axis=1,inplace=True)

      df = df.dropna()

      def clean(text): 
        text = text.lower()
        text = re.sub(r'@[A-Za-z0-9]+', '', text)
        text = re.sub(r'#[A-Za-z0-9]+', '', text)
        text = re.sub(r'_[A-Za-z0-9]+', '', text)
        text = re.sub(r'RT[\s]', '', text)
        text = re.sub(r'http\s+', '', text)
        text = re.sub(r'[0-9]+', '', text)
        text = re.sub(r'#[^A-Za-z]+', '', text)
    
      df['reviewContent'] = df['reviewContent'].apply(clean)
      df.to_csv('review.csv', index=False)


    outputFiles:
      - '*.csv'

Berikut adalah script python saya untuk sentimen analisis berdasarkan rating

id: sentimen
namespace: pretest2

inputs:
  - id: file
    type: FILE

tasks:
  - id: sentiment
    type: io.kestra.plugin.scripts.python.Script
    inputFiles: 
      input_csv: "{{ inputs.file }}"
    beforeCommands: 
      - pip install pandas
    script: |
      import pandas as pd

      df = pd.read_csv('{{ inputs.file }}')
  
      def klasifikasi(rating):
        if rating == 5:
          return 'Excellent'
        elif rating == 4:
          return 'Very good'
        elif rating == 3:
          return 'Good'
        elif rating == 2:
          return 'Bad'
        elif rating == 1:
          return 'Very bad'

      df['klasifikasi'] = df['rating'].apply(klasifikasi)
      df.to_csv('analisis_sentimen.csv', index=False)

    outputFiles:
      - '*.csv'
