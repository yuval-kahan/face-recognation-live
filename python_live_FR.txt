import threading
import cv2
from deepface import DeepFace  # Fixed typo

cap = cv2.VideoCapture(0, cv2.CAP_DSHOW)
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 640)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)
counter = 0
face_match = False
reference_img = cv2.imread("reference.jpg")

def check_face(frame):
    global face_match
    try:
        result = DeepFace.verify(frame, reference_img.copy())
        if result['verified']:  # Fixed the key name
            face_match = True
        else:
            face_match = False
    except Exception as e:  # Handle exceptions properly
        print("An error occurred:", e)

while True:
    ret, frame = cap.read()

    if ret:
        if counter % 30 == 0:
            try:
                thread = threading.Thread(target=check_face, args=(frame.copy(),))
                thread.start()  # Start the thread
            except Exception as e:  # Handle exceptions properly
                print("An error occurred:", e)
        counter += 1

        if face_match:
            cv2.putText(frame, "MATCH!", (20, 450), cv2.FONT_HERSHEY_SIMPLEX, 2, (0, 255, 0), 3)
        else:
            cv2.putText(frame, "no MATCH!", (20, 450), cv2.FONT_HERSHEY_SIMPLEX, 2, (0, 0, 255), 3)

        cv2.imshow("video", frame)

    key = cv2.waitKey(1)
    if key == ord("q"):
        break

cap.release()
cv2.destroyAllWindows()
