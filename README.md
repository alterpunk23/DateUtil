# DateUtil
Class for work with data in java

In Java, we play a lot with Dates. Here’s one more scenario. You have a string which has date in it. You want to convert it into a valid java.util.Date object. Now in Java you can convert a String to Date using SimpleDateFormat class. But we will add a little more complexity on that. Lets say the date format in String is not fixed. User might enter date in format “dd/mm/yyy” or “dd-mm-yyyy” or “dd.mmm.yy”! In any of such case we should get a valid java.util.Date object. Here’s a simple Util class that you can use to convert String having date in multiple formats to java.util.Date object.

public class DateUtil {
	// List of all date formats that we want to parse.
	// Add your own format here.
	private static List<SimpleDateFormat>; 
			dateFormats = new ArrayList<SimpleDateFormat>() {{
			add(new SimpleDateFormat("M/dd/yyyy"));
			add(new SimpleDateFormat("dd.M.yyyy"));
			add(new SimpleDateFormat("M/dd/yyyy hh:mm:ss a"));
			add(new SimpleDateFormat("dd.M.yyyy hh:mm:ss a"));
			add(new SimpleDateFormat("dd.MMM.yyyy"));
			add(new SimpleDateFormat("dd-MMM-yyyy"));
		}
	};

	/**
	 * Convert String with various formats into java.util.Date
	 * 
	 * @param input
	 *            Date as a string
	 * @return java.util.Date object if input string is parsed 
	 * 			successfully else returns null
	 */
	public static Date convertToDate(String input) {
		Date date = null;
		if(null == input) {
			return null;
		}
		for (SimpleDateFormat format : dateFormats) {
			try {
				format.setLenient(false);
				date = format.parse(input);
			} catch (ParseException e) {
				//Shhh.. try other formats
			}
			if (date != null) {
				break;
			}
		}

		return date;
	}
}

The above code has a list of SimpleDateFormat objects that holds different valid date formats that you want to parse. Add a new format if you want your code to parse it. Also check the convertToDate() method. It runs through each format from the list and check weather input string is valid date or not. If it is successful in converting String to Date, it returns the Date object. If it fails to convert String to Date then it returns null. Let’s test this with various input:

public class TestDateUtil {
	public static void main(String[] args) {
		System.out.println("10/14/2012" + " = " + DateUtil.convertToDate("10/14/2012"));
		System.out.println("10-Jan-2012" + " = " + DateUtil.convertToDate("10-Jan-2012"));
		System.out.println("01.03.2002" + " = " + DateUtil.convertToDate("01.03.2002"));
		System.out.println("12/03/2010" + " = " + DateUtil.convertToDate("12/03/2010"));
		System.out.println("19.Feb.2011" + " = " + DateUtil.convertToDate("19.Feb.2011" ));
		System.out.println("4/20/2012" + " = " + DateUtil.convertToDate("4/20/2012"));
		System.out.println("some string" + " = " + DateUtil.convertToDate("some string"));
		System.out.println("123456" + " = " + DateUtil.convertToDate("123456"));
		System.out.println("null" + " = " + DateUtil.convertToDate(null));

	}	
}

Output:
10/14/2012 = Sun Oct 14 00:00:00 CEST 2012
10-Jan-2012 = Tue Jan 10 00:00:00 CET 2012
01.03.2002 = Fri Mar 01 00:00:00 CET 2002
12/03/2010 = Fri Dec 03 00:00:00 CET 2010
19.Feb.2011 = Sat Feb 19 00:00:00 CET 2011
4/20/2012 = Fri Apr 20 00:00:00 CEST 2012
some string = null
123456 = null
null = null

Thus you can quickly check weather given string is a valid Date or not using DateUtil class.
Update: SimpleDateFormat and Thread Safety
Many developers have commented on this post about the issue of Thread safety in this code snippet. As SimpleDateFormat class itself is not thread safe it can cause issues in production. Here’s a workaround for this problem. Instead of SimpleDateFormat class in above utility method, use below ThreadSafeSimpleDateFormat class. This will solve the problem of Thread safety.

public class ThreadSafeSimpleDateFormat {

 private DateFormat df;

 public ThreadSafeSimpleDateFormat(String format) {
     this.df = new SimpleDateFormat(format);
 }

 public synchronized String format(Date date) {
     return df.format(date);
 }

 public synchronized Date parse(String string) throws ParseException {
     return df.parse(string);
 }
}
