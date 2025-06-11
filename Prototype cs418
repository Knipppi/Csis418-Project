import cv2
import numpy as np

# Defined colors.
lower_red1 = np.array([0, 70, 50])
upper_red1 = np.array([2, 255, 255])

lower_red2 = np.array([170, 70, 50])
upper_red2 = np.array([180, 255, 255]) 

lower_orange = np.array([6, 100, 100])
upper_orange = np.array([24, 255, 255]) 

lower_yellow = np.array([25, 100, 100]) 
upper_yellow = np.array([35, 255, 255])

lower_green = np.array([40, 40, 40])
upper_green = np.array([80, 255, 255])

lower_blue = np.array([100, 50, 50])
upper_blue = np.array([130, 255, 255])

lower_indigo = np.array([110, 50, 50])
upper_indigo = np.array([140, 255, 255])

lower_violet = np.array([140, 50, 50])
upper_violet = np.array([160, 255, 255])


# -User Input for Color Selection
print("Available colors: red, orange, yellow, green, blue, indigo, violet")
chosen_color_name = input("Enter the color you want to track: ").lower()

# Initialize target color arrays
lower_target_color = None
upper_target_color = None
is_red = False

if chosen_color_name == "red":
    lower_target_color = lower_red1
    upper_target_color = upper_red1
    is_red = True
elif chosen_color_name == "orange":
    lower_target_color = lower_orange
    upper_target_color = upper_orange
elif chosen_color_name == "yellow":
    lower_target_color = lower_yellow
    upper_target_color = upper_yellow
elif chosen_color_name == "green":
    lower_target_color = lower_green
    upper_target_color = upper_green
elif chosen_color_name == "blue":
    lower_target_color = lower_blue
    upper_target_color = upper_blue
elif chosen_color_name == "indigo":
    lower_target_color = lower_indigo
    upper_target_color = upper_indigo
elif chosen_color_name == "violet":
    lower_target_color = lower_violet
    upper_target_color = upper_violet
else:
    print(f"'{chosen_color_name}' is not a recognized color. Defaulting to tracking red.")
    lower_target_color = lower_red1
    upper_target_color = upper_red1
    is_red = True

# Input Video
print("Choose video source:")
print("1. Webcam")
print("2. Video file")
source_choice = input("Enter 1 or 2: ")

video_source = 0 # Default to webcam

if source_choice == '2':
    video_path = input("Enter the full path to the video file: ")
    video_source = video_path
elif source_choice != '1':
    print("Invalid choice. Defaulting to webcam.")

cap = cv2.VideoCapture(video_source)

if not cap.isOpened():
    print(f"Error: Could not open video source: {video_source}.")
    print("Webcam not connected, make sure it's connected.")
    exit()

# Tracking loop
while True:
    # Reads frame from video
    ret, frame = cap.read() 

    if not ret:
        print("End of video stream. Exiting.")
        break

    # Convert to HSV
    hsv_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

    # Mask for the chosen color
    if is_red:
        # For red, we need to create two masks and combine them
        mask1 = cv2.inRange(hsv_frame, lower_red1, upper_red1)
        mask2 = cv2.inRange(hsv_frame, lower_red2, upper_red2)
        mask = cv2.add(mask1, mask2)
    else:
        mask = cv2.inRange(hsv_frame, lower_target_color, upper_target_color)

    # Noise Reduction
    kernel = np.ones((5, 5), np.uint8)
    mask = cv2.erode(mask, kernel, iterations=1)
    mask = cv2.dilate(mask, kernel, iterations=1)

    # Contours
    contours_tuple = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    contours = contours_tuple[0] if len(contours_tuple) == 2 else contours_tuple[1]

    min_contour_area = 500

    if contours:
        for contour in contours:
            if cv2.contourArea(contour) > min_contour_area:
                # Bounding Box
                M = cv2.moments(contour)

                if M["m00"] != 0:
                    cX = int(M["m10"] / M["m00"]) 
                    cY = int(M["m01"] / M["m00"]) 

                    x, y, w, h = cv2.boundingRect(contour)

                    # Display Bounding Box
                    cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 2) # Green rectangle

                    # Display coordinates on the frame
                    cv2.putText(frame, f"X: {cX}, Y: {cY}", (cX - 50, cY - 20),
                                cv2.FONT_HERSHEY_SIMPLEX, 0.5, (255, 255, 255), 2)

                    print(f"Object detected at: X={cX}, Y={cY} (Color: {chosen_color_name})")

    # Display frames
    cv2.imshow("Original Frame", frame)
    cv2.imshow(f"{chosen_color_name.capitalize()} Color Mask", mask)
    
    # Exit if 'q' is pressed
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

# Release resources
cap.release() 
cv2.destroyAllWindows()
