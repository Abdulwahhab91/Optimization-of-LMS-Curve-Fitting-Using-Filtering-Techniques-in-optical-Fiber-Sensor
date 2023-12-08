This is the program to calculate the Brillouin gain spectrum (BGSs), and Brillouin frequency shift (BFS) by using the moving averaging filter(MAF), quadratic least squares (QLMS) method and moving median filter (MMF) for Brillouin optical fiber sensor and other distributed optical fiber sensor applications. The code is implemented into the paper published in the IEEE Sensor Journal. Feel free to use this code or the data, but please cite the following paper,
@code information based on the article {
author={Abdulwahhab Essa Hamzah},
title = {Fast and accurate measurement in BOTDA fibre sensor through the application of filtering techniques in frequency and time domains},
publisher = {IEEE Sensors Journal},
place={Bangi, Malaysia},
university={Universiti Kebangsaan Malaysia(UKM)},
year = {2023},
pages={2-4},
}


#define NUMBER_OF_SAMPLES	       300
#define NUMBER_OF_FREQUENCY	   201
#define window_size_MAF             12
#define window_size_MMF             28
double data[201][300] = {{----},{----},-----,{----}};
void process(){
	double fb;
	int Fmax, Fhigh;
	int max, half_max;
	int n = NUMBER_OF_FREQUENCY;

	double x[NUMBER_OF_FREQUENCY-1];    
	double y[NUMBER_OF_FREQUENCY-1];  
	double z[NUMBER_OF_FREQUENCY-1];
	double  A, a, b;
	double window[window_size_MMF];
	double filtered_signal[NUMBER_OF_SAMPLES];
	//double  F2, c;
	int s = 0;
	int i, j, k, l;
	double percent;
	int threshold, interval, point;
	int Flow = 0;

	l = 0;
	threshold = 0;// CHECK WITH
	interval = 1;
	point =0 ;	
	s = 0;				
	percent = 0.5;

	for (k = 0; k < NUMBER_OF_SAMPLES; k++)  
	{
		//---------------------------------------------
		//-------------Start the MAF filter--------------
		//---------------------------------------------
		for (i = 0; i < (n-1); i++) {   
			double average = 0.0;
			int start = i - window_size_MAF;  
			int end = i + window_size_MAF;  
			if (start < 0)
			{
				start = 0;
			}
			if (end >= (n-1))
			{
				end = (n-1) - 1;
			}
			for (int j = start; j <= end; j++)
			{
				average += data[j][k];
			}
			average /= (end - start + 1);
			x[i] = i;
			y[i] = average;

		}
		//---------------------------------------------
		//-------------end the MAF filter--------------
		//---------------------------------------------

		//----------------------------------------------------------------------
		//-------------Start finding the top half peak into the BGS-------------
		//----------------------------------------------------------------------
		//Find the max value
		max = 0;
		half_max = 0;
		for (i = s; i < (n-1); i++) {
			if (max < y[i]) {
				max = y[i];
				Fmax = i;
			}
		}

		if (max > threshold) {   //threshold=1
			while (1) {
				if (l == 0) {
					l = k * interval + point;
				}

				//Set the amount of percentage from max value
				half_max = max * (percent);  


				for (i = Fmax; half_max < y[i]; i--) //Search lower freq
{
					Flow = i;
					if (i == s)
						break;
				}

	for (i = Fmax; half_max < y[i]; i++)//Search higher freq 
{
					Fhigh = i;
					if (i == (n - 1))
						break;
				}
		//---------------------------------------------------------------------
		//------------end the finding of the top half peak into the BGS--------
		//---------------------------------------------------------------------



				//--------------------------------------------------------
				//--Start calculation to find the a, b and c coefficient--
				//-------------------------------------------------------
				a = (----);
				b = (----);
				//--------------------------------------------------------
				//-------------end the calculation of the coefficient-----
				//--------------------------------------------------------

				//--------------------------------------------------------
				//------------Start calculation the max peak -------------
				fb = A = 0;
				A = 1 / a;
				fb = -(A * b) / 2;
				//--------------------------------------------------------
				//-------------end calculation of the max peak -----------
				//--------------------------------------------------------
				if (fb > 0) {
					//record fb and FMAX
					fbArray[k] = fb;
					break;
				}
				percent -= 0.1;
			}
		}
	}
	//---------------------------------------------
	//-------------Start the MMF filter------------
	//---------------------------------------------

    for (i = 0; i < NUMBER_OF_SAMPLES; i++)
    {
        for (j = 0, k = i - (window_size_MMF / 2); j < window_size_MMF; j++, k++)
        {
            if (k < 0 || k >= NUMBER_OF_SAMPLES)
            {
                window[j] = 0; 
            }
            else
            {
                window[j] = fbArray[k];
            }
        }

        for (j = 0; j < window_size_MMF; j++)
        {
            for (k = j + 1; k < window_size_MMF; k++)
            {
                if (window[j] > window[k])
                {
                    double temp = window[j];
                    window[j] = window[k];
                    window[k] = temp;
                }
            }
        }

        // Set filtered sample to median of window
        filtered_signal[i] = window[window_size_MMF / 2]; 
        printf("%f\n",filtered_signal[i]);
    }
	//---------------------------------------------
	//-------------end the MMF filter--------------
	//---------------------------------------------

}

