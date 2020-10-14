/*
	Labirent
	08.07.2020
	Algoritma: Recursive (C#)
	
	Ali Eskici
	fb.com/4l135k1c1
	github/alieskici
*/


using System;
using System.Collections.Generic;
using System.Drawing;
using System.Windows.Forms;

namespace MAZE
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        int genislik = 400;
        int yukseklik = 400;

        Graphics gr;
        //Pen kalem = new Pen(Color.White, 2);
        SolidBrush firca = new SolidBrush(Color.Gray);
        Bitmap bmp;

        const int kare = 25;   //kare genisligi
        int bosluk = 10;
        bool[,] yer;    //kareler, ziyaret edilen true
        bool[,,] gel;   //gelis yonu; true; 0: Kuzey, 1: Bati, 2: Guney, 3: Dogu
        int sat, sut;   //matris boyutlari
        List<int> kom = new List<int>();    //boş komşular; true; 0: Kuzey, 1: Batı, 2: Güney, 3: Doğu
        
        Random r = new Random();
        int say, carpim;    //kare sayisi, sat * sut
        int kR, kC, kRy, kCy; //kare; satir, sutun, (y)eni kare satir sutun
        int sec;

        private void Form1_Load(object sender, EventArgs e)
        {
            p.Width = genislik;
            p.Height = yukseklik;

            bmp = new Bitmap(p.Width, p.Height);
            gr = Graphics.FromImage(bmp);

            sat = p.Height / kare;
            sut = p.Width / kare;

            yer = new bool[sat , sut];
            gel = new bool[sat, sut, 4];

            carpim = sat * sut;
        }

        private void Ciz()
        {
            //firca.Color = Color.FromArgb(r.Next(255), r.Next(255), r.Next(255));
            switch (sec)
            {
                case 0:
                    gr.FillRectangle(firca, kCy * kare, kRy * kare, kare - bosluk, kare*2 - bosluk);
                    break;
                case 1:
                    gr.FillRectangle(firca, kCy * kare, kRy * kare, kare * 2 - bosluk, kare - bosluk);
                    break;
                case 2:
                    gr.FillRectangle(firca, kC * kare, kR * kare, kare - bosluk, kare*2 - bosluk);
                    break;
                case 3:
                    gr.FillRectangle(firca, kC * kare, kR * kare, kare*2 - bosluk, kare - bosluk);
                    break;
                
                default:
                    break;
            }
            
            p.Image = bmp;
            Application.DoEvents();
            say++;
            System.Threading.Thread.Sleep(40);
        }

        private void KomsuBul()
        {
            kom.Clear();
            //Kuzey eklensin mi?
            if (kR > 0)
            {
                if (!yer[kR - 1, kC])
                {
                    kom.Add(0);
                }
            }
            //Bati eklensin mi?
            if (kC > 0)
            {
                if (!yer[kR, kC - 1])
                {
                    kom.Add(1);
                }
            }
            //Guzey eklensin mi?
            if (kR < sat - 1)
            {
                if (!yer[kR + 1, kC])
                {
                    kom.Add(2);
                }
            }
            //Dogu eklensin mi?
            if (kC < sut - 1)
            {
                if (!yer[kR, kC + 1])
                {
                    kom.Add(3);
                }
            }
        }

        private void BtnRun_Click(object sender, EventArgs e)
        {
            #region Clear
            gr.Clear(Color.White);
            p.Image = bmp;

            for (int i = 0; i < sat; i++)
            {
                for (int j = 0; j < sut; j++)
                {
                    yer[i, j] = false;

                    for (int k = 0; k < 4; k++)
                    {
                        gel[i, j, k] = false;
                    }
                }
            }
            #endregion

            //rasgele başlangıç noktası
            kR = r.Next(0, sat);
            kC = r.Next(0, sut);

            yer[kR, kC] = true;
            say = 1;

            do
            {
                KomsuBul();

                if (kom.Count > 0)
                {
                    sec = kom[r.Next(0, kom.Count)];
                    switch (sec)
                    {
                        case 0:
                            kRy = kR - 1;
                            kCy = kC;
                            break;
                        case 1:
                            kRy = kR;
                            kCy = kC - 1;
                            break;
                        case 2:
                            kRy = kR + 1;
                            kCy = kC;
                            break;
                        case 3:
                            kRy = kR;
                            kCy = kC + 1;
                            break;
                        default:
                            break;
                    }

                    gel[kRy, kCy, sec] = true;
                    yer[kRy, kCy] = true;

                    Ciz();
                }
                else
                {
                    //bir önceki kareye git
                    if (gel[kR, kC, 0])
                    {
                        kRy = kR + 1;
                        kCy = kC;
                    }
                    else if (gel[kR, kC, 1])
                    {
                        kRy = kR;
                        kCy = kC + 1;
                    }
                    else if (gel[kR, kC, 2])
                    {
                        kRy = kR - 1;
                        kCy = kC;
                    }
                    else if (gel[kR, kC, 3])
                    {
                        kRy = kR;
                        kCy = kC - 1;
                    }
                }
                
                kR = kRy; kC = kCy;
                
            } while (say < carpim);
        }
    }
}
