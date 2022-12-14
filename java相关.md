***快读快写***

```java
public class Main {
	public static void main(String[] args) {
		Input input = new Input();
		Output output = new Output();
		int n = input.nextInt();
		int m = input.nextInt();
		int[] strategy = new int[n];
		if (n == 2)
			strategy = new int[] {m - 1, m - 1};
		else if (n <= m) {
			for (int i = 0, j = m - 1; i < n; i++, j--)
				strategy[i] = j;
		} else if (m >= 4) {
			for (int i = 0; i < m; i++)
				strategy[i] = i;
		} else if (m == 3) {
			strategy[0] = 1;
			strategy[1] = 1;
			strategy[2] = 2;
			strategy[3] = 2;
		} else {
			strategy[0] = 1;
			strategy[1] = 1;
		}
		for (int e : strategy) {
			for (int i = 0; i < m; i++)
				output.append(e == i ? 1 : 0).append(' ');
			output.appendNewLine();
		}
		output.flush();
	}
}

class Input {
	private final byte[] buffer;
	private int pos;

	public Input() {
		try {
			buffer = new byte[System.in.available() + 2];
			buffer[buffer.length - 2] = '\r';
			buffer[buffer.length - 1] = '\n';
			System.in.read(buffer);
		} catch (Exception ex) {
			throw new RuntimeException(ex);
		}
	}

	public byte[] next(int n) {
		while (true) {
			byte b = buffer[pos++];
			if (b == '\r')
				pos++;
			else {
				pos--;
				break;
			}
		}
		byte[] bytes = new byte[n];
		System.arraycopy(buffer, pos, bytes, 0, n);
		pos += n;
		return bytes;
	}

	public byte[] next() {
		int from;
		while (true) {
			byte b = buffer[pos++];
			if (b == '\r')
				pos++;
			else if (b != ' ') {
				from = pos;
				break;
			}
		}
		byte[] bytes;
		while (true) {
			byte b = buffer[pos++];
			if (b == ' ') {
				bytes = new byte[pos - from];
				break;
			} else if (b == '\r') {
				bytes = new byte[pos++ - from];
				break;
			}
		}
		System.arraycopy(buffer, from - 1, bytes, 0, bytes.length);
		return bytes;
	}

	public byte[] nextLine() {
		int from = pos;
		while (true) {
			byte b = buffer[pos++];
			if (b == '\r')
				break;
		}
		byte[] bytes = new byte[pos++ - from - 1];
		System.arraycopy(buffer, from, bytes, 0, bytes.length);
		return bytes;
	}

	public byte nextChar() {
		while (true) {
			byte b = buffer[pos++];
			if (b == '\r')
				pos++;
			else if (b != ' ')
				return b;
		}
	}

	public int nextInt() {
		int n;
		boolean positive;
		while (true) {
			byte b = buffer[pos++];
			if (b == '-') {
				positive = false;
				n = buffer[pos++] - '0';
				break;
			} else if (b >= '0' && b <= '9') {
				positive = true;
				n = b - '0';
				break;
			}
		}
		while (true) {
			byte b = buffer[pos++];
			if (b >= '0' && b <= '9')
				n = n * 10 + b - '0';
			else {
				if (b == '\r')
					pos++;
				return positive ? n : -n;
			}
		}
	}

	public long nextLong() {
		long n;
		boolean positive;
		while (true) {
			byte b = buffer[pos++];
			if (b == '-') {
				positive = false;
				n = buffer[pos++] - '0';
				break;
			} else if (b >= '0' && b <= '9') {
				positive = true;
				n = b - '0';
				break;
			}
		}
		while (true) {
			byte b = buffer[pos++];
			if (b >= '0' && b <= '9')
				n = n * 10 + b - '0';
			else {
				if (b == '\r')
					pos++;
				return positive ? n : -n;
			}
		}
	}

	public double nextDouble() {
		long n;
		boolean positive;
		while (true) {
			byte b = buffer[pos++];
			if (b == '-') {
				positive = false;
				n = buffer[pos++] - '0';
				break;
			} else if (b >= '0' && b <= '9') {
				positive = true;
				n = b - '0';
				break;
			}
		}
		while (true) {
			byte b = buffer[pos++];
			if (b >= '0' && b <= '9')
				n = n * 10 + b - '0';
			else if (b == '.')
				break;
			else
				return positive ? n : -n;
		}
		long m = 0;
		long o = 1;
		while (true) {
			byte b = buffer[pos++];
			if (b >= '0' && b <= '9') {
				m = m * 10 + b - '0';
				o *= 10;
			} else {
				if (b == '\r')
					pos++;
				double d = n + (double)m / o;
				return positive ? d : -d;
			}
		}
	}
}

class Output {
	private static final int BUFFER_SIZE = 1048576;
	private final byte[] buffer = new byte[BUFFER_SIZE];
	private int pos;

	public Output append(String s) {
		int length = s.length();
		ensureCapacity(length);
		for (int i = 0; i < length; i++)
			buffer[pos++] = (byte)s.charAt(i);
		return this;
	}

	public Output append(byte[] bytes) {
		if (BUFFER_SIZE - pos < bytes.length) {
			flush();
			if (bytes.length > BUFFER_SIZE) {
				System.out.write(bytes, 0, bytes.length);
				return this;
			}
		}
		for (byte b : bytes)
			buffer[pos++] = b;
		return this;
	}

	public Output append(byte[] bytes, int from, int to) {
		int length = to - from;
		if (BUFFER_SIZE - pos < length) {
			flush();
			if (length > BUFFER_SIZE) {
				System.out.write(bytes, from, length);
				return this;
			}
		}
		for (int i = from; i < to; i++)
			buffer[pos++] = bytes[i];
		return this;
	}

	public Output append(char c) {
		ensureCapacity(1);
		buffer[pos++] = (byte)c;
		return this;
	}

	public Output append(int i) {
		return append(Integer.toString(i));
	}

	public Output append(long l) {
		return append(Long.toString(l));
	}

	public Output append(double d) {
		return append(Double.toString(d));
	}

	public void appendNewLine() {
		ensureCapacity(2);
		buffer[pos++] = '\r';
		buffer[pos++] = '\n';
	}

	public void flush() {
		System.out.write(buffer, 0, pos);
		pos = 0;
	}

	private void ensureCapacity(int n) {
		if (BUFFER_SIZE - pos < n)
			flush();
	}
}
```