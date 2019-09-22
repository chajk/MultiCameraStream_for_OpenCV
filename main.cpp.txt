#include <iostream>
#include <thread>
#include <vector>
#include <opencv2/opencv.hpp>

using namespace std;
using namespace cv;

void detect(Mat img, String strCamera);
void stream(String strCamera);

int main()
{
	// profile3: 640x360, h.264, latency is shorter than profile2 
	// which is same resolution. I don't know the reason 
	thread cam1(stream, "rtsp://admin:1234qwer@192.168.0.6:554/profile3/media.smp");

	// profile2: 640x360, h.264, originally tested, 
	thread cam2(stream, "rtsp://admin:1234qwer@192.168.0.6:554/profile2/media.smp");

	//thread cam1(0);// USB Camera-0 Stream
	//thread cam2(1);// USB Caemra-1 Stream

	cam1.join();
	cam2.join();
	return 0;
}

// Test
void detect(Mat img, String strCamera) {
	string cascadeName1 = "haar_cascade_for_people_detection.xml";
	CascadeClassifier detectorBody;
	bool loaded1 = detectorBody.load(cascadeName1);
	Mat original;
	img.copyTo(original);
	vector<Rect> human;
	
	cvtColor(img, img, COLOR_BGR2GRAY);// CV_BGR2GRAY);
	equalizeHist(img, img);
	detectorBody.detectMultiScale(img, human, 1.1, 2, 0 | 1, Size(40, 80), Size(400, 480));
	
	if (human.size() > 0)
	{
		for (int gg = 0; gg < human.size(); gg++)
		{
			rectangle(original, human[gg].tl(), human[gg].br(), Scalar(0, 0, 255), 2, 8, 0);
		}
	}
	
	imshow("Detect " + strCamera, original);
	int key = waitKey(40);
	//End of the detect
}

void stream(String strCamera) {
	VideoCapture cap(strCamera);
	if (cap.isOpened()) {
		while (true) {
			Mat frame;
			cap >> frame;
			
			/*
			resize(frame, frame, Size(640, 480));
			detect(frame, strCamera);
			imshow("Detect", frame);
			int key = waitKey(40);
			*/
			
			imshow("Steream " + strCamera, frame);
			
			int key = waitKey(1);
			if (key == 27) 
				break;
		}
	}
}

#if 0
#include <iostream>
#include <opencv2/opencv.hpp>
#include <opencv2/highgui/highgui.hpp>
//#include <opencv2/imgproc/imgproc.hpp>
//#include <opencv2/core/core.hpp>
#include <thread>

using namespace std;
using namespace cv;

int main()
{
	VideoCapture cap1;
	VideoCapture cap2;
	//VideoCapture cap3;
	//VideoCapture cap4;

	//cap1.open(0);
	cap1.open("rtsp://admin:1234qwer@192.168.0.5:554/profile2/media.smp");
	cap2.open("rtsp://admin:1234qwer@192.168.0.3:554/profile2/media.smp");
	//cap3.open("video3.mp4");
	//cap4.open("video4.mp4");

	/*
	cap1.set(CAP_PROP_FRAME_WIDTH, 640);
	cap1.set(CAP_PROP_FRAME_HEIGHT, 480);
	cap2.set(CAP_PROP_FRAME_WIDTH, 640);
	cap2.set(CAP_PROP_FRAME_HEIGHT, 480);
	cap3.set(CAP_PROP_FRAME_WIDTH, 640);
	cap3.set(CAP_PROP_FRAME_HEIGHT, 480);
	cap4.set(CAP_PROP_FRAME_WIDTH, 640);
	cap4.set(CAP_PROP_FRAME_HEIGHT, 480);
	*/

	if (!cap1.isOpened() || !cap2.isOpened()) { //|| !cap3.isOpened() || !cap4.isOpened()) {
		cout << "sorry try again" << endl;
		return -1;
	}

	namedWindow("video1", WINDOW_NORMAL);
	namedWindow("video2", WINDOW_NORMAL);
	//namedWindow("video3", WINDOW_NORMAL);
	//namedWindow("video4", WINDOW_NORMAL);

	Mat frame1, frame2;// , frame3, frame4;

	while (true)
	{
		bool s1 = cap1.read(frame1);
		bool s2 = cap2.read(frame2);
		//bool s3 = cap3.read(frame3);
		//bool s4 = cap4.read(frame4);

		if (!s1 || !s2) {
			cout << "sorry I can't read" << endl;
			break;
		}
		imshow("video1", frame1);
		imshow("video2", frame2);
		//imshow("video3", frame3);
		//imshow("video4", frame4);

		if (waitKey(10) == 27)
		{
			cout << "Goodbye" << endl;
			break;
		}
	}
	return 0;
#endif