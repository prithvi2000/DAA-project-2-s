# DAA-project-2-s
import java.util.Arrays;
import java.util.Comparator;
import java.util.Random;

class Point {
    double x, y;

    @Override
    public String toString() {
        return "Point{" +
                "x=" + x +
                ", y=" + y +
                '}';
    }

    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }
}

public class ClosestPair {

    // Brute-force approach to find the closest pair
    public static double closestBruteForce(Point[] points) {
        double minDist = Double.POSITIVE_INFINITY;

        for (int i = 0; i < points.length; i++) {
            for (int j = i + 1; j < points.length; j++) {
                double dist = distance(points[i], points[j]);
                if (dist < minDist) {
                    minDist = dist;
                }
            }
        }

        return minDist;
    }

    // Function to calculate the Euclidean distance between two points
    public static double distance(Point p1, Point p2) {
        return Math.sqrt((p2.y - p1.y) * (p2.y - p1.y) + (p2.x - p1.x) * (p2.x - p1.x));
    }

    // Generate random points with random x and y coordinates
    public static Point[] generateRandomPoints(int n) {
        Point[] points = new Point[n];
        Random random = new Random();

        for (int i = 0; i < n; i++) {
            double x = random.nextDouble() * 100;
            double y = random.nextDouble() * 100;
            points[i] = new Point(x, y);
        }

        return points;
    }
    // Divide-and-conquer approach to find the closest pair
    public static double closestDivideConquer(Point[] xSorted, Point[] ySorted) {
        int n = xSorted.length;

        // Base case: If there are 2 or 3 points, use the brute-force approach
        if (n <= 3) {
            return closestBruteForce(xSorted);
        }

        // Divide the points into left and right halves
        int mid = n / 2;
        Point[] xSortedLeft = Arrays.copyOfRange(xSorted, 0, mid);
        Point[] xSortedRight = Arrays.copyOfRange(xSorted, mid, n);

        // Create sorted arrays for the left and right halves
        Point[] ySortedLeft = new Point[mid];
        Point[] ySortedRight = new Point[n - mid];

        int leftIndex = 0;
        int rightIndex = 0;

        for (Point point : ySorted) {
            if (point.x <= xSorted[mid - 1].x) {
                ySortedLeft[leftIndex] = point;
                leftIndex++;
            } else {
                ySortedRight[rightIndex] = point;
                rightIndex++;
            }
        }

        // Recursively find the closest pairs in the left and right halves
        double leftResult = closestDivideConquer(xSortedLeft, ySortedLeft);
        double rightResult = closestDivideConquer(xSortedRight, ySortedRight);

        // Determine the minimum delta between left and right results
        double minResult = Math.min(leftResult, rightResult);

        // Find points within the strip that are closer than minResult
        Point[] strip = new Point[n];
        int stripSize = 0;

        for (Point point : ySorted) {
            if (Math.abs(point.x - xSorted[mid - 1].x) < minResult) {
                strip[stripSize] = point;
                stripSize++;
            }
        }

        // Sort the strip by y-coordinate
        Arrays.sort(strip, 0, stripSize, Comparator.comparingDouble(point -> point.y));

        // Check for closer points in the strip
        for (int i = 0; i < stripSize; i++) {
            for (int j = i + 1; j < Math.min(i + 7, stripSize); j++) {
                double dist = distance(strip[i], strip[j]);
                if (dist < minResult) {
                    minResult = dist;
                }
            }
        }

        return minResult;
    }

    public static void main(String[] args) {
        int[] intervals = {50,100,500,1000,10000,20000,30000,50000,80000,100000};
        long startTime,endTime,elapsedTimeNs;
        for(int i=0;i<intervals.length;i++) {
            // Generate random points with random x and y coordinates
            Point[] points = generateRandomPoints(intervals[i]);
//            System.out.println(Arrays.toString(points));

            // Sort points by x-coordinate
            Arrays.sort(points, Comparator.comparingDouble(point -> point.x));

            // Copy the sorted array for y-coordinate sorting
            Point[] ySorted = Arrays.copyOf(points, points.length);

            // Sort the copy by y-coordinate
            Arrays.sort(ySorted, Comparator.comparingDouble(point -> point.y));

            startTime = System.nanoTime();
            // Find the closest pair using divide-and-conquer
            double closestDistance = closestDivideConquer(points, ySorted);
            endTime=System.nanoTime();
            elapsedTimeNs = endTime - startTime;

//            System.out.println("Closest Pair Distance: " + closestDistance +"Elapsed Time:" + elapsedTimeNs);
            System.out.println(elapsedTimeNs);
        }
    }
}
