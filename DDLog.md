```
import CocoaLumberjack

class LogFormatter: DDDispatchQueueLogFormatter {

    let dateFormatter: DateFormatter

    override init() {
        dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "yyyy/MM/dd HH:mm:ss:SSS"
        super.init()
    }

    override func format(message logMessage: DDLogMessage!) -> String {

        var logLevel: String
        switch logMessage.flag {
        case DDLogFlag.error:
            logLevel = "E"
        case DDLogFlag.warning:
            logLevel = "W"
        case DDLogFlag.info:
            logLevel = "I"
        case DDLogFlag.debug:
            logLevel = "D"
        case DDLogFlag.verbose:
            logLevel = "V"
        default:
            logLevel = "U"  // Unknown
        }

        let dateAndTime = dateFormatter.string(from: logMessage.timestamp)
        let queueLabel = queueThreadLabel(for: logMessage)
        let fileName = (logMessage.file as NSString).lastPathComponent
        let lineNumber = logMessage.line
        let function = logMessage.function
        let message = logMessage.message

        return "\(dateAndTime) [\(logLevel)] [\(queueLabel)] [\(fileName):\(lineNumber) \(function)] \(message) "
    }
}


DDTTYLogger.sharedInstance?.logFormatter = LogFormatter()
DDLog.add(DDTTYLogger.sharedInstance as! DDLogger)
DDLogError("error message")
DDLogWarn("warn message")
DDLogInfo("info message")
DDLogDebug("debug message")
DDLogVerbose("verbose message")

```
