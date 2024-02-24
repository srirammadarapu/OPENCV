# OPENCV
import numpy as np
import cv2 as cv

video_path =r"C:\Users\nawaz\Videos\ir sample.mp4" # Replace with the actual path to your video file

cap = cv.VideoCapture(video_path)
ret, frame1 = cap.read()
ret, frame2 = cap.read()

while cap.isOpened():
    diff = cv.absdiff(frame1, frame2)
    gray = cv.cvtColor(diff, cv.COLOR_BGR2GRAY)
    blur = cv.GaussianBlur(gray, (5, 5), 0)
    _, thresh = cv.threshold(blur, 20, 255, 0)
    dia = cv.dilate(thresh, None, iterations=3)
    contours, _ = cv.findContours(dia, cv.RETR_TREE, cv.CHAIN_APPROX_SIMPLE)
    cv.drawContours(frame1, contours, -1, (0, 255, 0), 3)
    for c in contours:
        (x, y, w, h) = cv.boundingRect(c)
        if cv.contourArea(c) < 2700:
            continue
        cv.rectangle(frame1, (x, y), (x + w, y + h), (0, 255, 0), 2)
        cv.putText(frame1, "Status: Movement", (10, 20), 0, 1, (0, 0, 255), 3)
    cv.imshow("frame", frame1)
    frame1 = frame2
    ret, frame2 = cap.read()
    if cv.waitKey(1) & 0xFF == ord(' '):
        break

cv.destroyAllWindows()
cap.release()
