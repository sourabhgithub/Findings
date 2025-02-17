public class DecryptTest {

    private static final String CIPHER_ALGORITHM = "AES/CBC/PKCS5Padding";

    @Test
    public void testGetDecryptedString() throws Exception {
        // Generate a random AES key
        KeyGenerator keyGen = KeyGenerator.getInstance("AES");
        keyGen.init(256);
        Key key = keyGen.generateKey();

        // Generate a random IV
        byte[] ivBytes = new byte[16]; // AES uses 16-byte IV
        IvParameterSpec iv = new IvParameterSpec(ivBytes);

        // Encrypt a sample string
        String originalText = "Hello, World!";
        byte[] encryptedText = encrypt(originalText, key, iv);

        // Decrypt it back
        String decryptedText = Decrypt.getDecryptedString(encryptedText, key, ivBytes);

        // Verify the decrypted text matches the original text
        assertEquals(originalText, decryptedText);
    }

    private byte[] encrypt(String text, Key key, IvParameterSpec iv) throws Exception {
        Cipher cipher = Cipher.getInstance(CIPHER_ALGORITHM);
        cipher.init(Cipher.ENCRYPT_MODE, key, iv);
        return cipher.doFinal(text.getBytes());
    }
}
