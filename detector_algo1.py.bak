##
##  DESCRIPTION: Program to detect the motion from stationery or moving camera.
##  AUTHOR: Ajay
##  DATE: 25 Dec 14
##

import numpy as np
import cv2
# import video
import math
import time
from itertools import groupby as g


## TODO START:
# Check for the spreadness of the moving object, if small then display, else consider it as background
#
## TODO END:

## GLOBAL VARIABLES
PI = 3.142857


# Functions to find the most common angle
def most_common_angle(L):
    return max(g(sorted(L)), key=lambda(x, v):(len(list(v)),-L.index(x)))[0]
    
def find_common(current_angle):
    
    
    return max(set(current_angle), key=current_angle.count)

    
# Function to find the flow of objects moving, eliminating the background motion.
def draw_flow(img, flow, step=8):
    print "Start"
    # print "FLOW BEGIN"
    # print flow
    # print "END"
    h, w = img.shape[:2]
    y, x = np.mgrid[step/2:h:step, step/2:w:step].reshape(2,-1)
    fx, fy = flow[y,x].T	                                                                ##Probably T here represent the tail of the flow
    # print fx, fy
    lines = np.vstack([x, y, x+fx, y+fy]).T.reshape(-1, 2, 2)                   ##This may be creating the line from head to tail of the flow
    lines = np.int32(lines + 0.5)
    # lines = [0, 0, 1, 1]
    # print lines
    ####CAUTION: This is for 640*480 resolution camera.
    size_of_data = 4800
    j = 0
    x1 = []
    y1 = []
    x2 = []
    y2 = []
    p = 0
    h = 0
    factor1 = 4
    factor2 = 2
    sum_angle = 0
    current_angle = []
    # size_of_data = 3420
    for i in range(size_of_data):
    # while(fx[i]):
        # if(angle < -3):
        hypothenuse =  np.int32(np.sqrt(fy[i]*fy[i]+ fx[i]*fx[i]))
        # print "Angle: ", angle, " | ", fy[i], " , ", fx[i], " => ", hypothenuse, " | ",  y[i], " , ", x[i]
        
        if(hypothenuse > 4):
        
            # angle = math.atan2(fy[i], fx[i])
            # angle = (180/PI)*angle
            # angle = np.int32(angle)
            # print hypothenuse, "|", angle, "|", y[i], x[i]
            # print i, j
            angle = math.atan2(fy[i], fx[i])
            angle = (180/PI)*angle
            angle = np.int32(angle)
            current_angle.append(angle)
            sum_angle += angle
            
            # print angle
            x1.append(x[i])
            y1.append(y[i])
            j += 1
    
    # common_angle = find_common(current_angle)
    
    
    if(j!=0):
        common_angle = most_common_angle(current_angle)
        print "\tCmn: ", common_angle
        angle_variance = 0
        average_angle = sum_angle/j
        print "\tAvg: ", average_angle
        for k in range(j):
            angle_variance += (current_angle[k]-average_angle)*(current_angle[k]-average_angle)
            
        angle_variance /= j
        angle_std_deviation = np.sqrt(angle_variance)
        print "\tStd: ", angle_std_deviation
        if(angle_std_deviation < 20):
            print "\tBackground Movement"
            if(common_angle > average_angle):
                for p in range(j):
                    if(current_angle[p] < (average_angle-factor2*angle_std_deviation) or current_angle[p] > (average_angle+factor1*angle_std_deviation) ):
                        x2.append(x1[p])
                        y2.append(y1[p])
                        h += 1
            else:
                for p in range(j):
                    if(current_angle[p] > (average_angle+factor2*angle_std_deviation) or current_angle[p] < (average_angle-factor1*angle_std_deviation) ):
                        x2.append(x1[p])
                        y2.append(y1[p])
                        print "x: ", x2[h], "y: ", y2[h], "angle: ", current_angle[p]
                        h += 1
                        
        
        # elif(angle_std_deviation > 100):
        else:
            "\tFast Motion or Angle 180/-180 | => Too noisy"
            
        # elif(angle_std_deviation <40):
            # print "\tObject Found"
        # else:
            # print"\tObject Found"
    # print "X1: ", x1
    # print "Y1: ", y1
    # angle2 = fy[1]/fx[1]
    # angle3 = fy[2]/fx[2]
    
    # print angle2
    # print angle3
    # angle = np.vstack(fy/fx).T
    # print "ANGLE BEGIN"
    # print angle.T.reshape(2, -1)
    # print "END"
    # vis = cv2.cvtColor(img, cv2.COLOR_GRAY2BGR)
    vis = canvas = np.zeros((h, w, 3), dtype = "uint8")
    cv2.polylines(vis, lines, 10, (0, 255, 0))
    # for (x1, y1), (x2, y2) in lines:
        # cv2.circle(vis, (x1, y1), 1, (0, 0, 0), -1)
    print "Terminate"
    return vis, x2, y2, h
    # return vis, x1, y1, j

    
# MAIN 
if __name__ == '__main__':
    import sys
    try: fn = sys.argv[1]
    except: fn = 0

    # cam = video.create_capture(fn)
    cam = cv2.VideoCapture(fn)
    ret, prev = cam.read()
    prevgray = cv2.cvtColor(prev, cv2.COLOR_BGR2GRAY)
    show_hsv = False
    show_glitch = False
    cur_glitch = prev.copy()
    blue = (255, 0, 0)
    red = (0, 0, 255)
       
    while True:
        time1 = time.clock()
        ret, img = cam.read()
        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
        flow = cv2.calcOpticalFlowFarneback(prevgray, gray, 0.5, 3, 15, 3, 5, 1.2, 0)
        prevgray = gray
        vis, x1, y1, size_of_data2 = draw_flow(gray, flow)
        for i in range(size_of_data2):
            # img[y1[i], x1[i]] = (0, 255, 0)
            # cv2.circle(img, (x1[i], y1[i]), 2, red)
            cv2.rectangle(img, (y1[i]+1 , x1[i]+1), (y1[i]-1, x1[i]-1), blue, -1)
            
        cv2.imshow('detect', img)
        # cv2.imwrite('vis.jpg', vis)
        # vis = edge(vis)
        # cv2.imshow('flow', vis)
        
        ch = 0xFF & cv2.waitKey(1)
        # if ch == 'SpaceBar'
        
        if ch == 27:
            break
       
        time2 = time.clock()
        print time2 - time1
    cv2.destroyAllWindows()
